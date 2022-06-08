# static

**Contents**

### static 메소드와 일반 메소드의 차이

static 메소드와 일반 메소드의 차이는 무엇일까. 인스턴수 변수 `name`의 선언과 함께 그것을 출력하는 메소드를 아래와 같이 정의하였다. 하지만 이 소스는 컴파일시 에러를 발생한다. 

```java
public class ReferenceStatic {
	String name = "username1";
	
	// 중간 생략
	public static void staticMethodCallVariable() {
		System.out.pritnln(name);
	}
}
```

에러를 발생하는 이유는 `name` 이라는 변수가 `클래스 변수`가 아닌 인스턴스 변수이기 때문이다. 따라서 `name` 변수는 아래와 같이 선언해주어야 한다.

```java
public static String name = "username1"; // 클래스 변수 선언
```

이와 같이 클래스 변수로 선언한 `name` 은 모든 클래스 객체에 대해 하나의 값을 바라보게 된다. **static 메소드와** 일반(인스턴스) 메소드의 차이는 인스턴스 변수 사용 유무로 나뉜다.

### static의 생명주기

`static` 키워드를 사용하면 **객체가 생성되는 시점이 아닌** Class Loader 가 클래스를 Load 할 때 Data 영역에 메모리가 할당되게 된다. 이 영역은 같은 유형의 클래스마다 공유되며 Process 가 종료되는 시점에서 해제되므로 `static` 키워드의 생명주기 역시 Class Load 시 생성되고 Process 종료 시 해제되게 된다. 

### Singleton pattern with static

디자인 패턴 중 하나인 싱글톤 패턴은 `static` 의 개념을 이용한 패턴이다.

싱글톤은 단 하나의 객체만 생성하는 패턴이다.

싱글톤 클래스를 만드는 규칙은 다음과 같다.

1. 싱글톤 클래스의 생성자를 `private` 로 하여 외부에서 생성하지 못하게 한다.
2. 클래스 내부에 `static` 변수를 선언한다.
3. `getInstance()` 메소드를 `static` 으로 만들고 내부에서 싱글턴 객체를 선언하여 리턴한다.

```java
class Singleton {
  private static Singleton instance;

  // 생성자를 private 로 하여 외부에서 생성 불가능하게 만든다.
  private Singleton() { }

  public static Singleton getInstacne() {
    if (instance == null) {
      instance = new Singleton();
    }

    return instance;
  }
}
```