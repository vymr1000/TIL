## **프로세스 (process)**

프로세스의 사전적 의미를 찾아보면 아래와 같이 정의를 내린다.

> *컴퓨터에서 연속적으로 실행되고 있는 컴퓨터 프로그램*
> 

<br/>

## Thread의 의미

Thread의 사전적 의미를 찾아보면 아래와 같이 정의를 내린다.

> *프로세스 내에서 실행되는 여러 흐름의 단위*
> 

- 자바프로세스가 시작되고 `main()` 메소드가 수행되면서 하나의 쓰레드가 시작된다
- 자바를 이용하여 웹을 제공할때에는 Tomcat같은 WAS를 사용하는데 이 WAS도 똑같이 `main()` 메소드에서 생성한 쓰레드들이 수행되는 것
- 많은 쓰레드가 필요하다면 이 `main()` 메소드에서 쓰레드를 생성해주면 된다
- 프로세스의 종료
    - 싱글 쓰레드: 메인 쓰레드가 종료하면 프로세스도 종료된다
    - 멀티 쓰레드: 실행 중인 쓰레드가 하나라도 있다면 프로세스는 종료되지 않는다

<br/>

## Thread의 특징

- 프로세스 내에서 각각 `Stack`만 따로 할당을 받고 `Code`, `Data`, `Heap` 영역을 공유한다.
- 공유하는 메모리로 인해 독립적인 프로세스와는 다르게 쓰레드간 데이터를 주고 받는게 간단해지고 시스템 자원 소모가 줄어들게 된다.
- 공유하는 메모리로 인해 Context Switching에 대한 오버헤드가 줄어든다.

<br/>

## Thread를 생성하는 방법

쓰레드를 생성하는 방법은 크게 두 가지가 있다.

- `Thread` 클래스를 사용하는 방법
- `Runnable` 인터페이스를 이용하는 방법

`Runnable` 인터페이스와 `Thread` 클래스는 모두 `java.lang` 패키지에 있다. 따라서 이 인터페이스나 클래스를 사용할 때는 별도로 import할 필요가 없다. `Runnable` 인터페이스에 선언되어 있는 메소드는 단지 `run()` 하나이다. `run()` 메소드는 쓰레드를 통해 작업하고 싶은 내용이 들어가게 된다.

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

**Thead와 Runnable 실행방법의 차이**

두개의 예제 모두 간단하게 `run()` 메소드를 정의하였고 `RunnableSample`, `ThreadSample` 클래스 모두 쓰레드로 실행할 수 있다는 공통점이 있다. 하지만 이 두개의 클래스를 실행하는 방식은 다르다. 아래 예제는 쓰레드를 수행하는 `RunTreads`라는 클래스이다.

```java
public class RunThreads {
	public static void main(String[] args) {
		RunThreads threads = new RunThreads();
		threads.runBasic();
	}
}

public void runBasic() {

	// Runnable 인터페이스를 구현한 쓰레드 시작
	RunnableSample runnable = new RunnableSample();
	new Thread(runnable).start();

	// Thread 클래스를 상속받은 쓰레드 시작
	ThreadSample thread = ThreadSample();
	thread.start();
}

```

Runnable을 구현한 부분을 보면 `new Thread(runnable).start();` 라고 나와있는거 처럼 쓰레드로 바로 시작할 수는 없다. 따라서 Thread 클래스의 생성자에 해당 객체를 추가하여 시작해주어야만 한다. 쓰레드를 시작하는 메소드는 `start()` 이다.

**Thread 클래스와 Runnable 인터페이스, 언제 쓸까**

인터페이스와 클래스 각 객체가 `start()` 메소드를 호출하게 되면서 쓰레드가 시작된다. 쓰레드가 다른 클래스를 확장할 필요가 있을때 `Runnable` 인터페이스르 구현하면 되며 그렇지 않은 경우에는 `Thread`클래스를 사용하는 것이 편하다.

<br/>

### **Thread를 실행할 때 start()와 run()의 차이**

run()을 호출하는 것은 생성된 스레드 객체를 실행하는 것이 아니라, 단순히 스레드 클래스 내부의 run 메서드를 실행시키는 것이다.

즉, main 함수의 스레드를 그대로 사용해서 run 메서드를 실행하기 때문에 **새로운 스레드가 생기지 않고 병렬처리를 할 수 없다**.

반면에 start()는 새로운 스레드를 실행하는데 필요한 호출 스택(call stack)을 생성한 다음에 run을 호출해서, 생성된 호출 스택에 run()이 첫 번째로 저장되게 한다.

좀 더 쉽게 말하면, start()를 호출하면 스레드를 새롭게 생성해서 해당 스레드를 runnable 한 상태로 만든 후 run() 메서드를 실행하게 된다. 따라서 **start()를 호출해야만 멀티스레드로 병렬 처리가 가능해진다.**

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

<br/>

## 데몬쓰레드

- 주 쓰레드의 백그라운드에서 작업을 돕는 보조적인 역할을 수행하는 쓰레드.
- 주 스레드가 종료되면 데몬 스레드는 강제적으로 자동 종료된다.
- JVM은 데몬쓰레드의 종료를 기다리지 않는다.

```java
public void runDaemonThread() {
	ThreadSample thread = new ThreadSample();
	thread.setDaemon(true); // 데몬쓰레드
	thread.start();
}
```
<br/>

## **스레드를 많이 띄어야 하는 상황 (I/O가 걸릴때마다)**

- 대기 시간 즉 cpu가 i/o처리 시 block 걸리는 시간이 길 때 (ex, 대용량 파일 I/O 발생, 긴 네트워크 지연 발생)에는 스레드 개수를 늘려서 대기시간(block io 시간)을 줄여야 한다.

<br/>

## 동시성과 병렬성

| 동시성 | 병렬성 |
| --- | --- |
| 동시에 실행되는 것 같이 보이는 것 | 실제로 동시에 여러 작업이 처리되는 것 |
| 싱글 코어에서 멀티 쓰레드(Multi thread)를 동작 시키는 방식 | 멀티 코어에서 멀티 쓰레드(Multi thread)를 동작시키는 방식 |
| 한번에 많은 것을 처리 | 한번에 많은 일을 처리 |
| 논리적인 개념 | 물리적인 개념 |