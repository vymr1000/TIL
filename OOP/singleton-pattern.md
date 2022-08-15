## Singleton Pattern (싱글턴 패턴)

싱글턴 패턴이란 인스턴스를 하나만 만들어 사용하기 위한 패턴이다. 커넥션 풀, 스레드 풀, 디바이스 설정 객체 등과 같은 경우 인스턴스를 여러개 만들게 되면 불필요한 자원을 사용하게 되고, 또 프로그램이 예상치 못한 결과를 낳을 수 있다. 싱글턴 패턴은 오직 인스턴스를 하나만 만들고 그것을 계속해서 재사용 한다. 싱글턴 패턴을 적용할 경우, 의미상 두 개의 객체가 존재할 수 없다. 이를 구현하려면 객체 생성을 위한 new에 제약을 걸어야 하고, 만들어진 단일 객체를 반환할 수 있는 메서드가 필요하다.

 <br/>

### 싱글턴 패턴 예제 1

```java
package singletonPattern

public class Singleton {
	static Singleton singletonObject;
	// private 생성자
	private Singleton() {}; 
	
	// 객체 반환 정적 메서드
	public static Singleton getInstance() {
		if (singletonObject == null) {
			singletonObject = new Singleton();
		}

		return singletonObject;
	}
}
```

<br/>

싱글턴 패턴의 특징을 아래와 같이 정리할 수 있다.

- private 생성자를 갖는다.
- 단일 객체 참조 변수를 갖는다.
- 단일 객체 참조변수가 참조하는 단일 객체를 반환하는 `getInstance()` 정적 메서드를 갖는다.
- 단일 객체는 쓰기 가능한 속성을 갖지 않는 것이 정석이다.