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

<br/>
## Thread 예제

**Thread를 상속받은 ModifyAmountThread 클래스**

```java
public class ModifyAmountThread extends Thread {
    private  CommonCalculate calc;
    private boolean addFlag;

    public ModifyAmountThread(CommonCalculate calc, boolean addFlag){
        this.calc = calc;
        this.addFlag = addFlag;
    }
    public void run() {
        for(int loop=0; loop<10000; loop++) {
            if(addFlag) {
                calc.plus(1);
            } else {
                calc.minus(1);
            }
        }
    }
}
```

**연산을 정의해놓은 CommonCalculate 클래스**

```java
public class CommonCalculate {
    private int amount;
    public CommonCalculate() {
        amount = 0;
    }
		// 방법 1. synchronized 동기화 - 메소드 전체
		public synchronized void plus(int value) {
        amount+=value;
    }
    public synchronized void minus(int value) {
        amount-=value;
    }

		/*
		
		방법.2 synchronized 블록을 통해 동기화 처리 할 부분을 지정.
		
		private Object lock = new Object();

		...
		public void plus(int value) {
        synchronized (lock) {
            amount+=value;
        }
    }
    public void minus(int value) {
        synchronized (lock) {
            amount-=value;
        }
    }

		*/
    public int getAmount() {
        return amount;
    }
}
```

**Thread 생성 및 실행 클래스**

```java
public class RunSync {
    public static void main(String[] args) {
        RunSync runSync = new RunSync();
        runSync.runCommonCalculate();
    }

    private void runCommonCalculate() {
        CommonCalculate calc = new CommonCalculate();

				// 쓰레드1, 쓰레드2 생성 동일한 calc 객체를 사용한다.
        ModifyAmountThread thread1 = new ModifyAmountThread(calc, true);
        ModifyAmountThread thread2 = new ModifyAmountThread(calc, true);

        thread1.start();
        thread2.start();

        try {
						// thread1, thread2가 종료될때까지 메인쓰레드를 대기시킨다.
						// join을 통해 쓰레드가 수행하는 연산에 대한 결과를 볼 수 있다.
            thread1.join();
            thread2.join();
            System.out.println("Final Value is " + calc.getAmount());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
