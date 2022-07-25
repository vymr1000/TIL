# Java 8에서 새로운 것들

## Lambda & Functional Interface

### [Notion: 🙊Lambda 참조](https://www.notion.so/Lambda-5690eafe0630418597eea68d43060680)

<br/>

## Optional

공식문서에서 Optional의 정의는 아래와 같이 되어있다.

> *A container object which may or may not contain a non-null value. If a value is present, isPresent() will return true and get() will return the value. null이 아닌 값을 포함하거나 포함하지 않을 수 있는 컨테이너 객체입니다. 값이 있으면 isPresent()는 true를 반환하고 get()은 값을 반환합니다.*
> 

클래스 선언부는 아래와 같다.

```java
public final class Optional<T> {
		private static final Optional<?> EMPTY = new Optional<>();
		private final T value;
		...
}
```

`Optional` 클래스는 기본적으로 `Object` 을 상속 받았으며 `final` 로 선언되어 있고 제네릭 클래스이다. final 로 선언되어 있다는 것은 어떤 의미일까.

final 로 선언된 변수는 변경이 불가능 하지만 final 클래스라고해서 변경이 불가능 한 것은 아니다. 대신 추가적인 확장이 불가능하다. 즉, **자식클래스를 만들 수 없다는 의미**다. `Optional` 클래스는 null처리를 보다 간편하게 하기 위해서 만들어졌다. 자칫 잘못하면 NPE가 발생할 수 있는데, 이 문제를 보다 간편하고 명확하게 처리하려면 `Optional`을 사용하면 된다.

<br/>

### Optional 생성

****값이 Null인 경우, Optional.empty()****

```java
Optional<String> optional = Optional.empty();

System.out.println(optional); // Optional.empty
System.out.println(optional.isPresent()); // false
```

****값이 Null이 아닌 경우, Optional.of()****

만약 어떤 데이터가 절대 null이 아니라면 `Optional.of()`로 생성할 수 있다. 만약 `Optional.of()`로 null을 저장하려고 하면 `NullPointerException`이 발생한다.

```java
Optional<String> optional = Optional.of("MyName");
```

****값이 Null일수도, 아닐수도 있는 경우, Optional.ofNullbale()****

만약 어떤 데이터가 null이 올 수도 있고 아닐 수도 있는 경우에는 `Optional.ofNullbale`로 생성할 수 있다. 그리고 이후에 `orElse` 또는 `orElseGet` 메소드를 이용해서 **값이 없는 경우라도 안전하게** 값을 가져올 수 있다.

```java
// Optional의 value는 값이 있을 수도 있고 null 일 수도 있다.
Optional<String> optional = Optional.ofNullable(getName());
String name = optional.orElse("anonymous"); // 값이 없다면 "anonymous" 를 리턴
```

<br/>

### Optional 사용법 예시

예를 들어 아래와 같은 우편번호를 꺼내는 null 검사 코드가 있다고 하자. 이 코드는 null 검사 때문에 상당히 복잡하다.

```java
// Optional 적용 전 
public String findPostCode() {
    UserVO userVO = getUser();
    if (userVO != null) {
        Address address = user.getAddress();
        if (address != null) {
            String postCode = address.getPostCode();
            if (postCode != null) {
                return postCode;
            }
        }
    }
    return "우편번호 없음";
}
```

하지만 Optional을 사용하면 이러한 코드를 아래와 같이 표현할 수 있다.

```java
public String findPostCode() {
    // 위의 코드를 Optional로 펼쳐놓으면 아래와 같다.
    Optional<UserVO> userVO = Optional.ofNullable(getUser());
    Optional<Address> address = userVO.map(UserVO::getAddress);
    Optional<String> postCode = address.map(Address::getPostCode);
    String result = postCode.orElse("우편번호 없음");

    // 그리고 위의 코드를 다음과 같이 축약해서 쓸 수 있다.
    String result = user.map(UserVO::getAddress)
        .map(Address::getPostCode)
        .orElse("우편번호 없음");
}
```

<br/>

### 결론

Optional은 null 또는 값을 감싸서 NPE(NullPointerException)로부터 부담을 줄이기 위해 등장한 Wrapper 클래스이다. Optional은 값을 Wrapping하고 다시 풀고, null 일 경우에는 대체하는 함수를 호출하는 등의 오버헤드가 있으므로 잘못 사용하면 시스템 성능이 저하된다. **그렇기 때문에 메소드의 반환 값이 절대 null이 아니라면 Optional을 사용하지 않는 것이 좋다. 즉, Optional은 메소드의 결과가 null이 될 수 있으며, null에 의해 오류가 발생할 가능성이 매우 높을 때 반환값으로만 사용되어야 한다.**

<br/>
<br/>

## Default Method

인터페이스에서 원래는 메소드 선언부만 존재해야 하지만 메소드에 default 키워드가 있으면 메소드 구현부가 포함될 수 있다.

```java
public interface DefaultStaticInterface {
	static final String name = "What is Default Method?";
	static final int since = 2022;
	String getName();
	int getSince();
	default String getEmail() {
		return name + "@naver.com";
	}
}
```

인터페이스를 구현하는 클래스를 정의할 때 `getEmail()` 메소드를 구현하지 않아도 컴파일 단계에서 에러가 발생하지 않는다.

```java
public class DefaultImplementChild implements DefaultStaticInterface {
	@Override
	public String getName() {
		return name;
	}

	@Override
	public int getSince() {
		return int since;
	}
}
```

default 키워드를 왜 사용할까. 그것은 바로 “하위호환성” 때문인데, 예를 들어 오픈소스 코드에 존재하는 인터페이스에 메소드를 추가한다고 생각해보자. 인터페이스에 메소드를 추가함으로써 해당 오픈소스 인터페이스를 사용하는 모든 사용자가 에러를 발생하고 수정을 해야되는 일이 발생할 수 있다. 이럴때 default 메소드를 사용한다면 해당 인터페이스에 메소드를 추가할 수 있다.

<br/>
<br/>

## Reference

[참고: Tistory [Java] Optional이란?](https://mangkyu.tistory.com/70)