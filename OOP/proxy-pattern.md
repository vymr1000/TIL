## Proxy Pattern (프록시 패턴)

프록시는 대리자, 대변인이라는 뜻을 가진 단어다. 대리자/대변인이라고 하면 다른 누군가를 대신해 그 역할을 수행하는 존재를 말한다. 아래 예제는 프록시를 적용하지 않은 코드이다.

```java
package proxyPattern;

public class Service {
	public String runSomething() {
		return "return Something";
	}
}
```

```java
package proxyPattern;

public class ClientWithNoProxy {
	public static void main(String[] args) {
		// Proxy를 이용하지 않은 직접 호출
		Service service = new Service();
		System.out.println(service.runSomething());
	}
}
```
프록시 패턴의 경우 **실제 서비스 객체가 가진 메서드와 같은 이름의 메서드를 사용하는데**, 이를 위해 인터페이스를 사용한다. 인터페이스를 사용하면 서이브 객체가 들어갈 자리에 대리자 객체를 대신 투입해 **클라이언트 쪽에서는 실제 서비스 객체를 통해 메서드를 호출라고 리턴값을 받는지, 대리자를 통해 메서드를 호출하고 리턴값을 받는지 모른다.** 

```java
package proxyPattern;

public interface IService {
	String runSomething();	
}
```

```java
package proxyPattern;

public class Service implements ISerive {
	public String runSomething() {
		return "return Something";
	}
}
```

```java
package proxyPattern;

public class Proxy implements IService {
	IService service1;

	public String runSomething() {
		System.out.println("프록시는 호출에 대한 흐름 제어가 목적이다");

		service1 = new Service();
		return service1.runSomething();
	}
}

```

```java
package proxyPattern;

public class ClientWithProxy {
	public static void main(String[] args) {
		// Proxy를 이용한 호출
		IService proxy = new Proxy();
		System.out.println(proxy.runSomething());
	}
}
```

```java
public class Store {
	Payment payment;

	public Store(Payment payment) {
		this.payment = payment;
	}

	public void buySomething(int amount) {
		payment.pay(amount);
	}
}
```

```java
public class Cash implement Payment {
	@Override
	public void pay(int amount) {
		System.out.println(amount + " 현금결제");
	}
}
```

```java

// Proxy 역할을 하는 클래스
public class CashPerf implements Payment {
	Payment cash = new Cash();

	@Override
	public void pay(int amount) {
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();

		cash.pay(amount);

		stopWatch.stop();
		System.out.println(stopWatch.prettyPrint());
	}
} 
```

```java
// Client 코드
public class StoreTest {

	@Test
	public void testPay() {

		//Payment cash = new Cash();

		Payment cashPerf = new CashPerf();
		Store store = new Store(cashPerf);
		store.buySomething(35000);
	}
}
```

<br/>

## Reference

[https://refactoring.guru/design-patterns/proxy](https://refactoring.guru/design-patterns/proxy)