# @Transactional에 대해서

## 같은 클래스 내 메소드 호출시 트랜잭션 적용 이슈 
### StockService.java
```java
	@Transactional
	public void updateStock(Stock stock) {
		stockRepository.save(stock);
		System.out.println("Entity Manager contains stock true/false =  " + em.contains(stock));
	}

	/**
	 * decrease() -> updateStock() 호출
	 */
	public void decrease(Long id, Long quantity) {
		Stock stock = stockRepository.findById(id).orElseThrow();
		stock.decrease(quantity);
		updateStock(stock);
	}
```

### StockServiceTest.java
```java
@SpringBootTest
class StockServiceTest {

	@Autowired
	private StockService stockService;

	@Autowired
	private StockRepository stockRepository;

	@BeforeEach
	public void insert() {
		// stock 재고를 100으로 초기 세팅한다.
		Stock stock = new Stock(1L, 100L);
		stockRepository.save(stock);
	}

	@Test
	public void multi_thread_test() throws InterruptedException {
		// 100개의 요청, 32개의 쓰레드.
		int threadCount = 100;
		// 비동기로 실행하는 작업을 단순화하여 사용할 수 있게 하는 Java API ExecutorService.
		ExecutorService executorService = Executors.newFixedThreadPool(32);
		// 100개의 요청이 끝날때까지 기다리는 다른 스레드에서 실행중인 작업이 완료될 때까지 기다리도록 하는 클래스 CountDownLatch.
		CountDownLatch latch = new CountDownLatch(threadCount);

		// threadCount 만큼 반복 호출.
		for (int i = 0; i < threadCount; i++) {
			executorService.submit(() -> {
				try {
					stockService.decrease(1L, 1L);
				} finally {
					latch.countDown();
				}
			});
		}

		latch.await();
		// stock을 조회한다.
		Stock stock = stockRepository.findById(1L).orElseThrow();
		// 100에서 100만큼 반복되어 0인지 검증한다.
		assertEquals(0, stock.getQuantity());
	}

}
```

`updateStock()` 메소드에만 `@Transactional`이 적용된 상태이다. 그리고 테스트 코드에서 `decrease()`를 호출해서 해당 메소드로부터 `updateStock()`을 **자기 호출**하였다. 그리고 `updateStock()`은 트랜잭션이 적용된 상태이기에 영속성 컨텍스트가 존재해야 하고 **영속성 컨텍스트가 존재한다면 JPA를 통해 조회 혹은 저장 시에 1차 캐시에 보관된다는 것을 알 수 있다.**

```java
System.out.println("Entity Manager contains stock true/false =  " + em.contains(stock));
// Entity Manager contains stock true/false = false
```

하지만 위의 코드를 통해 엔티티 매니저(영속성 컨텍스트의 주체)가 stock를 1차 캐시로 보관하고 있는지 살펴봤지만 `false`가 떨어졌다. 그 말은 트랜잭션이 존재하지 않아 영속성 컨텍스트도 존재하지 않기 때문이다. 그러므로 해당 기능이 동작하고 있지 않아 stock이 존재하지 않는다는 뜻이다.

트랜잭션이 제대로 동작한다면 부모로부터 트랜잭션을 전파받기 때문에 `updateStock()`에 `@Transactional`을 지우더라도 트랜잭션 기능이 동작해서 true가 나온다. 이런 경우엔 부모 메소드에서 예외가 발생하더라도 하위 메소드의 작업까지 롤백된다. 또 하위 메소드에서도 예외가 발생하면 롤백마크가 적용이 되어 부모 메소드까지 롤백되므로 트랜잭션 기능이 올바르게 작동한다.

---

## 트랜잭션과 Proxy

@Transactional은 AOP를 사용하여 Proxy의 형태로 동작한다. Proxy 형태로 동작하기 때문에 같은 클래스안의 메소드에서 다른 메소드를 호출했을때 중첩 Transaction이 동작하지 않음 → 무조건 진입점의 Transaction기준으로 동작한다.

**AOP(Aspect Oriented Programming)**

> 관점지향 프로그래밍이라는 뜻으로 여러 곳에서 사용되는 공통된 로직을 모듈화하여 비즈니스 로직에서 분리시켜준다. 이로써 우리는 비즈니스 로직 외에 부가적인 로직은 따로 외부에서 관리하여 유지보수 및 재사용성이 용이해진다.
>

**프록시(Proxy)**

> 스프링의 AOP는 프록시를 활용하여 구현을 하고있다. 어떠한 클래스가 AOP 대상일 시 원본 클래스 대신 프록시가 감싸진 클래스가 자동으로 만들어져 프록시 클래스가 빈에 등록이 된다. 이렇게 빈에 등록된 프록시 클래스는 원본 클래스가 호출될 시 자동으로 바꿔서 사용해준다.
>

<img src="./images/proxy.png" width="400px"/>

외부메소드인 C에서 A메소드를 호출할 시 프록시가 감싸져있는 A메소드를 호출하게 된다. 반면, 내부 메소드인 B메소드에서 A메소드를 호출할 시 Proxy를 호출하게 되는 게 아닌 직접적으로 A메소드를 호출하여 프록시가 발동하지 못하게 된다.


## 결론
@Transactional은 AOP를 사용하여 프록시의 형태로 동작한다. 
AOP를 적용하면 스프링은 **대상 객체 대신에 프록시를 스프링 빈으로 등록하게 되는데 이렇게 되면** 스프링은 의존관계 주입시에 실제 객체 대신에 프록시 객체를 주입하게 된다. 
프록시 객체가 주입되기 때문에 대상 객체를 외부에서 호출할 때에는 문제가 발생하지 않지만  **대상 객체의 내부에서 자신의 다른 메서드 호출이 발생하면 프록시를 거치지 않고 대상 객체를 직접 호출하기 때문에 트랜잭션이 적용되지 않는 문제가 발생한다**. 
위 코드에서도 `decrease()`  내부에서 @Transactional이 적용되어 있는 `updateStock()` 를 호출하기 때문에 동일한 현상이 발생한다.


부모 메소드인 트랙잭션만 적용한다고 동시성 문제가 해결되지는 않는데 이유는 프록시에서 커밋하기 전에 다른 스레드가 해당 자원에 접근할 수 있기 때문이다.

<br />
<br />
<br />

## Reference 
[[Spring] 프록시 내부 호출 문제점](https://hyuuny.tistory.com/186)    
[Proxy형태로 동작하는 JPA @Transactional](https://cobbybb.tistory.com/17)  
[동시성(Concurrency) 이슈와 JPA Lock 메커니즘의 오해](https://stir.tistory.com/251)
