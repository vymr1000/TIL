### 예외처리 기본규칙

- 예외는 잡아서 `catch`하거나 `throw` 한다
- 예외를 잡거나 `throw` 할때 지정한 예외뿐 아니라 해당 예외의 자식도 함께 처리된다.
    - 예를들어 `Exception` 를 `catch` 로 잡으면 그 하위 예외도 모두 잡을 수 있다
    - 예들들어 `Exception` 를 `throws` 로 던지면 그 하위 예외도 모두 던질 수 있다.

<br/>

### 예외처리 & 예외던짐 흐름

<img src="./images/exception.jpg" width="500px"/>

- **예외처리**는 5번 예외를 처리하게 되면 애플리케이션 로직이 정상흐름으로 마무리 된다.
- **예외던짐**은 예외를 처리하지 못할 경우로 호출된 곳으로 예외를 계속 던지는 모습이다.

<br/>

### 예외가 발생했을때 진행순서

1. try블록의 실행이 중단된다.
2. catch 블록 중에 발생한 예외를 처리할 수 있는 블록이 있는지 찾는다.
3. 예외를 처리할 수 있는 catch 블록이 없다면 
    - finally 블록을 실행한 후 한 단계 높은 try 블록으로 전달한다.
4. 예외를 처리할 수 있는 catch 블록이 있다면 
    - 해당 catch 블록안의 코드 실행 → finally 블록 실행 → try 블록 이후의 코드 실행 순서로 이어진다.
    
<br/>

## 체크 예외(Checked Exception)

체크 예외는 RuntimeException의 하위 클래스가 아니면서 Exception 클래스의 하위 클래스들이다. `체크 예외의 특징은 반드시 에러 처리를 해야하는 특징(try/catch or throw)`을 가지고 있다.

- `존재하지 않는 파일의 이름을 입력(FileNotFoundException)`
- `실수로 클래스의 이름을 잘못 적음(ClassNotFoundException)`

<br/>

## 언체크 예외(Unchecked Exception)

언체크 예외는 RuntimeException의 하위 클래스들을 의미한다. 이것은 체크 예외와는 달리 에러 처리를 강제하지 않다. 말 그대로 `실행 중에(runtime)` 발생할 수 있는 예외를 의미한다.

- `배열의 범위를 벗어난(ArrayIndexOutOfBoundsException)`
- `값이 null이 참조변수를 참조(NullPointerException)`

<br/>

### 다중 catch 블록 작성시 주의점

- 부모 예외 클래스가 자식보다 먼저 나오면 안된다.

```java
try {
	...
} catch (Exception e) {
	...
} catch (FileNotFoundException e) {
	...
} finally {

}
```

<br/>

### throws 구문

```java
/* throws 구문구조: [메서드 선언부] throws [예외 클래스명] */
public User findUser (String username) throws UserNotFoundException {}
```

아래와 같은 메소드 선언부 옆에 반드시 추가해야된다. 추가하지 않으면 컴파일 오류발생

- checked 예외를 던지는 메소드
- 다른 메소드에서 발생한 checked 예외를 처리하지 않는 메소드