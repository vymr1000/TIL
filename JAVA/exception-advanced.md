## Java Exception 주의할 점

### 예외상황 (Exceptional Condition)

- 예외를 학습하면서 가장 빠지기 쉬운 오류는 **예외를 처리하는 방법**에 대해 집중한다는 것이다. 하지만 더 중요한 것은 **예외상황을 만들지 않는 코드를 작성하는 것**이다.
- Exception 이라고 하는 예외상황은 예측이 가능한 상황이다. 또한, 프로그램 실행중에 기본적으로는 일어나지 말아야할 상황이다. **프로그램을 하나의 함수집합이라고 생각을 했을때 함수 제 각각의 기능을 수행하는데 있어서 정상적으로 작동하지 못하게 되는 상황을 의미한다.** 즉, 정상적인 상황이라면 발생하지 상황이지만 일어날 가능성이 있는 상황이고 그것을 처리해줘야되는 상황인 것이다.

<br/>

### 예측하지 못한 오류상황

예외상황은 예측 가능한 상황이라고 하면 예측하지 못한 오류상황은 무엇인가. 그것은 버그라고 말할 수 있을 것이다. 예측하지 못했다면 그 상황에 대해 처리코드 또한 없을 것이고 프로그래머는 그 오류 상황에 대해 제대로 처리하는 코드를 작성하여 다시 빌드해야될 것이다. 즉, 원래 프로그램에 들어가야할 기능인데 없는 것이라고 볼 수 있다. 프로그램 실행도중에 발생하는 에러는 어쩔 수 없지만 예외는 프로그래머가 이에 대한 처리를 미리 해주어야 한다. 예외처리란 프로그램 실행시 발생할 수 있는 예기치 못한 예외의 발생에 대비한 코드를 작성하는 것이며 예외처리의 목적은 예외의 발생으로 인한 실행 중인 프로그램의 갑작스런 비정상 종료를 막고 정상적인 실행상태를 유지할 수 있도록 하는 것이다. 발생한 예외를 처리하지 못하면 프로그램은 비정상적으로 종료되며 처리되지 못한 예외는 JVM의 예외처리기가 받아서 예외의 원인을 화면에 출력한다. 

<br/>

### 예외는 진짜 예외 상황에만 사용하라

- 제어흐름용으로 예외를 사용해선 안된다

<br/>

### 복구할 수 있는 상황에는 체크 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라

- 이는 체크 예외와 런타임 예외를 구분하는 가장 기본적인 규칙이다. 체크 예외를 던지면 호출하는 쪽이 그 예외를 catch로 잡아 처리하거나 외부로 전파하도록 강제하게 된다. API 설계자는 API 사용자에게 체크 예외를 던져주어 그 상황에서 회복해내라고 요구한 것이다. 물론 사용자는 예외를 catch만 하고 별다른 조치를 취하지 않을 수 있지만 이는 보통 좋지 않은 생각이다. 

<br/>

### e.**printStackTrace**()를 사용하지 마라

- 개발시에만 사용할 것, 불필요한 로그 출력으로 메모리 누수 발생

<br/>

## 예외처리의 의미

예외와 예외 처리를 더 잘 이해하기 위해 현실세계에 빗대어 생각해보자. 사용자가 온라인으로 어떤 제품을 주문했는데 배송 도중에 어떠한 사건으로 인하여 배송에 실패하는 상황이 벌어졌을때, 좋은 회사는 이 상황을 처리하고 상품(패키지)이 제 시간에 도착할 수 있도록 패키지를 정상적으로 다시 배송처리할 것이다. 프로그램도 마찬가지로 코드를 실행하는 동안 오류(실패상황)가 발생할 수 있다. 좋은 예외 처리는 뜻밖의 상황을 바로 처리하고 사용자에게 원하는 기능을 그대로 제공하기 위해 프로그램을 정상적으로 재라우팅할 수 있다.

일반적으로 시스템에 별 문제가 없다면 파일 시스템은 사용자가 원하는 파일을 가지고 있으며, 네트워크는 정상이고, JVM에는 항상 충분한 메모리가 있는 상황일 것이다. 이러한 상황을 ‘Happy path’이라고 부른다. 하지만 중요한 것은 시스템이 언제나 안정적이진 않다는 것이다. 파일시스템에 파일이 없을 수 있으며, 네트워크에 장애가 생길 수 있고, JVM 메모리가 부족한 상황이 있을 수 있다. 코드의 안정성은 이러한 ‘Unhappy path’인 상황을 어떻게 다루냐에 달려있다. ‘Unhappy path’한 상황은 말그대로 애플리케이션의 흐름에 부정적인 영향을 미치고 예외를 형성하기 때문에 이러한 상황을 처리해야 합니다. 아래 코드를 예시를 확인해보자.

```java
public static List<Player> getPlayers() throws IOException {
    Path path = Paths.get("players.dat");
    List<String> players = Files.readAllLines(path);

    return players.stream()
      .map(Player::new)
      .collect(Collectors.toList());
}
```

이 코드는 IOException을 처리하지 않고 `throws` 를 함으로서 예외를 `call stack` 위로 전달하도록 되어있다. 이상적인 환경에서는 코드가 제대로 문제없이 작동할 것이다. 하지만 players.dat가 없으면 어떤 일이 벌어질까.

```java
Exception in thread "main" java.nio.file.NoSuchFileException: players.dat <-- players.dat file doesn't exist
    at sun.nio.fs.WindowsException.translateToIOException(Unknown Source)
    at sun.nio.fs.WindowsException.rethrowAsIOException(Unknown Source)
    // ... more stack trace
    at java.nio.file.Files.readAllLines(Unknown Source)
    at java.nio.file.Files.readAllLines(Unknown Source)
    at Exceptions.getPlayers(Exceptions.java:12) <-- Exception arises in getPlayers() method, on line 12
    at Exceptions.main(Exceptions.java:19) <-- getPlayers() is called by main(), on line 19
```

예외를 처리하지 않고 call stack위로 계속 전달할 경우 정상적인 프로그램이 완전히 실행을 멈출 수 있다. 이 때문에 개발자는 코드에 문제상황이 발생할 때를 대비한 로직이 있는지 체크해야 한다. 또한 여기서 예외에 대한 또 다른 이점이 있다는 점에 주목해야 한다. 바로 stack trace 이다. 이 stack trace으로 인해 디버거를 연결하지 않고도 문제가 되는 코드를 정확히 찾아낼 수 있다.

### ****Exception의 종류는 3가지로 구분된다.****

- Checked Exception
- Runtime Exception / Unchecked Exception
- Errors

### Checked Exception

체크 예외는 Java 컴파일러 단계에서 처리해야 하는 예외이다. call stack 호출부 위로 에 예외를 선언적으로 던지거나 try-catch문과 같은 구문으로 예외를 잡아 직접 처리해야 한다. 

> *Here's the bottom line guideline: If a client can reasonably be expected to recover from an exception, make it a checked exception. If a client cannot do anything to recover from the exception, make it an unchecked exception.* - The Java™ Tutorials
> 

[공식 튜토리얼 문서](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)에서는 메소드 호출자가 복구할 수 있을 것으로 판단할 수 있는 경우 체크 예외를 사용하도록 하고 예외를 처리하기 위해 아무것도 할 수 없으면 확인되지 않은 예외로 만들도록 안내하고 있다. 체크 예외의 대표적인 예는 IOException가 있다.

### Runtime Exception / Unchecked Exception

런타임 예외는 Java 컴파일러에서 처리할 필요가 없는 예외이다. 간단히 말해서 RuntimeException을 상속하는 예외를 생성하면 이 예외는 언체크(런타임) 예외가 된다. 그렇지 않으면 체크 예외이다. 처리할 필요가 없다는 것이 편리하게 들리지만 [공식 튜토리얼 문서](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)에서는 situational error(체크 예외)와 usage error(언체크 예외)를 구분하는 것과 같이 두개의 개념을 만든것에 대해 모두 합당한 이유가 있다고 말한다. 런타임 예외의 대표적인 예는 NullPointerException 이다.

### Errors

오류는 무한 재귀 호출 또는 메모리 누수와 같은 심각하고 일반적으로 복구할 수 없는 조건을 나타낸다. 그리고 RuntimeException을 상속하지 않더라도 언체크 예외이다. 예를 들어 애플리케이션이 입력을 위해 파일을 성공적으로 열었지만 하드웨어 또는 시스템 오작동으로 인해 파일을 읽을 수 없을 때 읽기에 실패하면 `java.io.IOError`가 발생한다. 오류(Error)는 캐치 또는 지정 요구사항의 적용을 받지 않는다. 오류의 대표적인 예는 OutOfMemoryError이다.

### Exception handling

예외 처리에 대해 본격적으로 알아보자. 

1. **throws**
    
    예외를 ‘처리’하는 가장 간단한 방법은 예외를 다시 throw하는 것이다.
    
    ```java
    public int getPlayerScore(String playerFile)
      throws FileNotFoundException {
     
        Scanner contents = new Scanner(new File(playerFile));
        return Integer.parseInt(contents.nextLine());
    }
    ```
    
    `FileNotFoundException`은 체크 예외이기 때문에 `throws` 명시를 통해 컴파일에러를 발생하지 않을 수 있는 가장 간단한 방법이지만 `getPlayerScore`를 호출하는 부분에서 이 예외를 처리해야 함을 의미한다.
    
    `parseInt`는 `NumberFormatException`을 던질 수 있지만 체크 예외가 아니기 때문에 따로 명시적으로 처리할 필요가 없다.
    

1. **try-catch**
    
    예외를 직접 처리하고 싶다면 `try-catch` 블록을 사용할 수 있다. 예외를 다시 throw하여 처리할 수 있다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            Scanner contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException noFile) {
            throw new IllegalArgumentException("File not found");
        }
    }
    ```
    
    혹은 예외 복구 절차로 진행할수도 있다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            Scanner contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } catch ( FileNotFoundException noFile ) {
            logger.warn("File not found, resetting score.");
            return 0;
        }
    }
    ```
    
2. **finally**
    
    예외 발생 여부와 상관없이 실행해야 하는 코드가 있을 때가 있는데, 여기에서 `finally` 키워드가 나온다. 지금까지의 예에서 숨어 있는 버그가 있었다. 바로 Java가 기본적으로 운영체제에 파일 스트림을 반환하지 않는다는 것이다. 코드에서 파일을 읽을 수 있든 없든 개발자는 스트림에 대한 적절한 정리를 직접 명시해야 한다. 먼저 ‘lazy’한 방식을 살펴본다.
    
    ```java
    public int getPlayerScore(String playerFile)
      throws FileNotFoundException {
        Scanner contents = null;
        try {
            contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } finally {
            if (contents != null) {
                contents.close();
            }
        }
    }
    ```
    
    여기에서 `finally` 블록은 파일 read를 시도할 때 성공 여부에 관계없이 실행되기를 원하는 코드를 나타낸다. `FileNotFoundException`이 call stack에서 발생하더라도 Java는 throw 하기 전에 `finally`의 내용을 호출한다. 
    
    예외를 catch 문에서 처리하면서 리소스를 `close()` 할 수도 있다. 아래 예시를 보자.
    
    ```java
    public int getPlayerScore(String playerFile) {
        Scanner contents;
        try {
            contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException noFile ) {
            logger.warn("File not found, resetting score.");
            return 0; 
        } finally {
            try {
                if (contents != null) {
                    contents.close();
                }
            } catch (IOException io) {
                logger.error("Couldn't close the reader!", io);
            }
        }
    }
    ```
    
    하지만 주의할 점은 `close()` 또한 ’risky’한 부분이 있기 때문에 예외처리를 해야한다. 이것은 꽤 복잡해 보일 수 있지만 발생할 수 있는 각각의 잠재적인 문제를 올바르게 처리하려면 꼭 작성해줘야 하는 부분이다.
    
3. **try-with-resources**
    
    다행스럽게도 Java 7부터는 `AutoCloseable`을 상속하는 클래스를 다룰 때 위의 구문을 단순화할 수 있다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile))) {
          return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException e ) {
          logger.warn("File not found, resetting score.");
          return 0;
        }
    }
    ```
    
    `AutoClosable`인 reference를 try 구문`()` 괄호 안에서 작성하면 리소스를 직접 닫을 필요가 없다. 하지만 여전히 `finally` 블록을 사용하여 원하는 다른 종류의 정리를 수행할 수도 있다.
    
4. ****Multiple *catch* Blocks**
    
    때때로, 코드에서 하나 이상의 예외를 던질 수 있고 우리는 각각 개별적으로 하나 이상의 catch 블록을 처리할 수 있다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile))) {
            return Integer.parseInt(contents.nextLine());
        } catch (IOException e) {
            logger.warn("Player file wouldn't load!", e);
            return 0;
        } catch (NumberFormatException e) {
            logger.warn("Player file was corrupted!", e);
            return 0;
        }
    }
    ```
    
    다중 catch는 필요에 따라 각 예외를 다르게 처리할 수 있다. 또한 여기에서 우리는 `FileNotFoundException`을 catch하지 않았는데, 이는 해당 예외가 `IOException`을 상속받기 때문이다. `IOException`을 잡기 때문에 Java는 `FileNotFoundException` 포함한 모든 하위 클래스도 처리되는 것으로 간주한다. 그러나 `FileNotFoundException`을 보다 일반적인 `IOException`과 는 다르게 처리해야 한다고 가정해 보자.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile)) ) {
            return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException e) {
            logger.warn("Player file not found!", e);
            return 0;
        } catch (IOException e) {
            logger.warn("Player file wouldn't load!", e);
            return 0;
        } catch (NumberFormatException e) {
            logger.warn("Player file was corrupted!", e);
            return 0;
        }
    }
    ```
    
    하위 클래스 예외를 개별적으로 처리하려면 예제에서 보이는 것과 같이 catch 목록 상위에 배치해야 한다.
    
5. ****Union *catch* Blocks**
    
    서로 다른 예외가 처리하는 방식이 동일할 것이라는 것을 알고 있을 때 Java 7은 동일한 블록에서 여러 예외를 catch하는 기능을 도입했다. | 기호를 통해 catch 문에 함께 넣어준다. 
    
    ```java
    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile))) {
            return Integer.parseInt(contents.nextLine());
        } catch (IOException | NumberFormatException e) {
            logger.warn("Failed to load score!", e);
            return 0;
        }
    }
    ```
    

### Throwing Exceptions

예외를 스스로 처리하고 싶지 않거나 다른 사람이 처리할 예외를 생성하려면 throw 키워드에 익숙해져야 한다. 직접 정의한 다음과 같은 체크 예외로 생각해보자.

```java
public class TimeoutException extends Exception {
    public TimeoutException(String message) {
        super(message);
    }
}
```

그리고 로직을 수행하는게 꽤 많은 시간이 걸리는 메소드가 있다고 가정해보자.

```java
public List<Player> loadAllPlayers(String playersFile) {
    // ... potentially long operation
}
```

1. **Throwing a Checked Exception**
    
    메소드에서 리턴하듯이 어느 시점에서든 예외를 던질 수 있다. 주의해야할 점은 예외 상황이라는 것을 나타내려고 할 때 throw 해야 한다는 것이다. (예외 throw를 조건문처럼 인식해서는 안된다)
    
    ```java
    public List<Player> loadAllPlayers(String playersFile) throws TimeoutException {
        while ( !tooLong ) {
            // ... potentially long operation
        }
        throw new TimeoutException("This operation took too long");
    }
    ```
    
    `TimeoutException`이 확인되었으므로 메소드 호출자가 처리할 수 있도록 메소드 끝에 `throws` 키워드도 사용해야 한다.
    

1. *****Throw*ing an Unchecked Exception**
    
    인풋 유효성 검사와 같은 작업을 수행하려는 경우 런타임 예외를 사용할 수 있다.
    
    ```java
    public List<Player> loadAllPlayers(String playersFile) throws TimeoutException {
        if(!isFilenameValid(playersFile)) {
            throw new IllegalArgumentException("Filename isn't valid!");
        }
       
        // ...
    }
    ```
    
    `IllegalArgumentException` 은 런타임 예외이므로 메소드 끝 `throws` 에 해당 예외를 명시할 필요가 없다.
    
2. ****Wrapping and Rethrowing****
    
    catch한 예외를 다시 throw 할 수도 있다.
    
    ```java
    public List<Player> loadAllPlayers(String playersFile) 
      throws IOException {
        try { 
            // ...
        } catch (IOException io) { 		
            throw io;
        }
    }
    ```
    
    혹은 특정 예외로 감싸서 던진다.
    
    ```java
    public List<Player> loadAllPlayers(String playersFile) 
      throws PlayerLoadException {
        try { 
            // ...
        } catch (IOException io) { 		
            throw new PlayerLoadException(io);
        }
    }
    ```
    
    이것은 다양한 예외를 **하나로 통합**하는 데 유용할 수 있다.
    
3. ****Rethrowing *Throwable* or *Exception***
    
    주어진 코드 블록에서 발생할 수 있는 유일한 예외가 런타임 예외인 경우 메서드 메소드 끝에 명시하지 않고 Throwable 또는 Exception을 catch하고 다시 throw할 수 있습니다.
    
    ```java
    public List<Player> loadAllPlayers(String playersFile) {
        try {
            throw new NullPointerException();
        } catch (Throwable t) {
            throw t;
        }
    }
    ```
    
    당연하게도 위의 코드는 체크 예외를 throw할 수 없으며 그 때문에 체크 예외를 다시 throw하더라도 서명을 throw 절로 표시할 필요가 없습니다. 이것은 프록시 클래스 및 메소드에서 편리합니다. 
    

### ****Anti-Patterns****

1. ****Swallowing Exceptions****
    
    예외를 처리하지 않고 Swallowing 하지 말자.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (Exception e) {} // catch and swallow
        return 0;
    }
    ```
    
    위의 예제는 예외를 소외 삼킨다고(Swallowing) 하는 것이다. 대부분의 경우 이렇게 하는 것은 문제를 해결하지 못하고 다른 코드에서도 문제를 해결할 수 없게 하기 때문에 문제가 있다.
    
    결코 일어나지 않을 것이라고 확신하는 체크 예외가 있는 경우가 있다. 그러한 경우에도 적어도 의도적으로 예외를 Swallowing했다는 주석을 추가해야 한다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            // this will never happen
        }
    }
    ```
    
    예외를 ‘Swallowing’ 하는 또 다른 예는 예외를 오류 스트림에 ‘출력만’하는 것이다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (Exception e) {
            e.printStackTrace();
        }
        return 0;
    }
    ```
    
    로그는 로거를 통해 기록하도록 하자.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            logger.error("Couldn't load the score", e);
            return 0;
        }
    }
    ```
    
    `e.printStackTrace()` 방식으로 예외를 처리하는 것이 매우 편리하지만 메소드 호출자가 문제를 해결하는 데 사용할 수 있는 중요한 정보를 삼키지 않도록 해야 한다. 이것은 결국 새로운 예외를 던질 때 예외를 원인으로 포함하지 않음으로써 실수로 예외를 삼킬 수 있다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            throw new PlayerScoreException(); // 반드시 매개변수로 e를 넣어주자.
        }
    }
    ```
    
    여기에서 호출자에게 오류를 알리기 위한 IOException을 원인을 포함하지 못했다. 이 때문에 개발자는 문제를 진단하는 데 사용할 수 있는 중요한 정보를 잃어버렸다. 반드시 매개변수로 e를 넣어주자.
    
2. ****Using *return* in a *finally* Block**
    
    예외를 삼키는 또 다른 방법은 finally 블록에서 `return`하는 것입니다. 이것은 코드에서 예외가 발생하더라도 갑자기 `return`함으로써 JVM이 예외를 삭제하기 때문에 좋지 않다.
    
    ```java
    public int getPlayerScore(String playerFile) {
        int score = 0;
        try {
            throw new IOException();
        } finally {
            return score; // <== the IOException is dropped
        }
    }
    ```
    
3. ****Using *throw* in a *finally* Block**
    
    `finally`블록에서 `return`을 사용하는 것과 유사하게 `finally`블록에서 throw된 예외는 catch 블록에서 발생하는 예외보다 우선시된다. 이렇게 하면 try 블록에서 원래 예외가 ‘erase’되고 중요한 정보가 모두 손실된다. finally 블록에서 throw하지 말자.
    
    ```java
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch ( IOException io ) {
            throw new IllegalStateException(io); // <== eaten by the finally
        } finally {
            throw new OtherException();
        }
    }
    ```
    
4. ****Using *throw* as a *goto***
    
    goto 문으로 throw를 사용하지 말자.
    
    ```java
    public void doSomething() {
        try {
            // bunch of code
            throw new MyException();
            // second bunch of code
        } catch (MyException e) {
            // third bunch of code
        }		
    }
    ```
    
    코드가 오류 처리와 반대로 흐름 제어에 예외를 사용하려고 하기 때문에 이것은 올바른 사용방법이 아니다.
    

### 예외가 발생했을 때 순서

1. try블록의 실행이 중단된다.
2. catch 블록 중에 발생한 예외를 처리할 수 있는 블록이 있는지 찾는다.
3. 예외를 처리할 수 있는 catch 블록이 없다면 
    - finally 블록을 실행한 후 한 단계 높은 try 블록으로 전달한다.
4. 예외를 처리할 수 있는 catch 블록이 있다면 
    - 해당 catch 블록안의 코드 실행 → finally 블록 실행 → try 블록 이후의 코드 실행 순서로 이어진다.
    

### Reference

[https://www.baeldung.com/java-exceptions](https://www.baeldung.com/java-exceptions)