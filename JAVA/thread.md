## Thread의 의미

- 자바프로세스가 시작되고 `main()` 메소드가 수행되면서 하나의 쓰레드가 시작된다
- 자바를 이용하여 웹을 제공할때에는 Tomcat같은 WAS를 사용하는데 이 WAS도 똑같이 `main()` 메소드에서 생성한 쓰레드들이 수행되는 것
- 많은 쓰레드가 필요하다면 이 `main()` 메소드에서 쓰레드를 생성해주면 된다
- 프로세스의 종료
    - 싱글 쓰레드: 메인 쓰레드가 종료하면 프로세스도 종료된다
    - 멀티 쓰레드: 실행 중인 쓰레드가 하나라도 있다면 프로세스는 종료되지 않는다

<br/>

## Thread를 생성하는 방법

쓰레드를 생성하는 방법은 크게 두 가지가 있다.

- `Thread` 클래스를 사용하는 방법
- `Runnable` 인터페이스를 이용하는 방법

`Runnable` 인터페이스와 `Thread` 클래스는 모두 `java.lang` 패키지에 있다. 따라서 이 인터페이스나 클래스를 사용할 때는 별도로 import할 필요가 없다. `Runnable` 인터페이스에 선언되어 있는 메소드는 단지 `run()` 하나이다.

<br/>

**Runnable 구현 클래스**

```java
public class RunnableSample implements Runnable {
	public void run() {
		// 쓰레드가 실행할 코드
	}
}
```

**Thead 상속 클래스**

```java
public class ThreadSample extends Thread {
	public void run() {
		// 쓰레드가 실행할 코드
	}
}
```

두개의 예제 모두 간단하게 `run()` 메소드를 정의하였고 `RunnableSample`, `ThreadSample` 클래스 모두 쓰레드로 실행할 수 있다는 공통점이 있다. 하지만 이 두개의 클래스를 실행하는 방식은 다르다. 아래 예제는 쓰레드를 수행하는 `RunTreads`라는 클래스이다.

```java
public class RunThreads {
	public static void main(String[] args) {
		RunThreads threads = new RunThreads();
		threads.runBasic();
	}
}

public void runBasic() {

	// Runnable 인터페이스 쓰레드 시작
	RunnableSample runnable = new RunnableSample();
	new Thread(runnable).start();

	// Thread 클래스 쓰레드 시작
	ThreadSample thread = ThreadSample();
	thread.start();
}

```

인터페이스와 클래스 각 객체가 `start()` 메소드를 호출하게 되면서 쓰레드가 시작된다. 쓰레드가 다른 클래스를 확장할 필요가 있을때 `Runnable` 인터페이스르 구현하면 되며 그렇지 않은 경우에는 `Thread`클래스를 사용하는 것이 편하다.