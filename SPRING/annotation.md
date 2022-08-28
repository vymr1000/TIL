# Annotation

## @AllArgsConstructor

- 클래스에 존재하는 모든 필드에 대한 생성자를 자동으로 생성한다.
- 이와 비슷한 @RequriedArgsConstructor도 있다. 초기화 되지 않은 모든 final 필드, @NonNull과 같이 제약조건이 설정되어있는 모든 필드들에 대한 생성자를 자동으로 생성한다.
- 발생할 수 있는 문제 상황
    - 예로 들어, 두 개의 같은 타입 인스턴스 멤버를 선언한 상황에서 개발자가 선언된 인스턴스 멤버의 순서를 바꾸면, 개발자도 인식하지 못하는 사이에 lombok이 생성자의 파라미터 순서를 필드 선언 순서에 따라 변경하게 된다. 이때, IDE가 제공해주는 리팩토링은 전혀 동작하지 않고, 두 필드가 동일 타입이기 때문에 기존 소스에서도 오류가 발생하지 않아 아무런 문제없이 동작하는 것으로 보이지만, 실제로 입력된 값이 바뀌어 들어가는 상황이 발생한다

<br/>

**문제상황 예시코드**

```java
@AllArgsConstructor
public static class Person {
    private String firstName;
    private String lastName;
}
// 성은 권, 이름은 현수
Person me = new Person("권", "현수");
```

```java
@AllArgsConstructor
public static class Person {
    private String lastName;
    private String firstName;
}
```

위 클래스에 대해 자동으로 `firstName`, `lastName`순서로 인자를 받는 생성자가 만들어진다. 그런데 누군가 lastName이 성인줄 알고 순서를 다음과 같이 바꾼다고 가정해보자 이 경우, IDE가 제공해주는 **리팩토링이 전혀 작동하지 않고**, **lombok이 개발자도 인식하지 못하는 사이에 생성자의 파라미터 순서를 필드 선언 순서에 맞춰 변경해버린다.**

대신, 생성자를 직접 만들고 필요한 경우에 직접 만든 생성자에 `@Builder`어노테이션을 붙이는 것을 권장한다. 그럼 파라미터 순서가 아닌 이름으로 값을 설정하기 때문에 리팩토링에 유연하게 대응할 수 있다.

```java
@AllArgsConstructor
public static class Person {
    private String firstName;
    private String lastName;
    
    @Builder
    private Person(String firstName, String lastName){
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

<br/> 
<br/> 

## @RequiredArgsConstructor

- final 필드에 대해 생성자를 만들어주는 lombok의 annotation.
- Spring Framework의 DI(의존성주입) 중 Constructor Injection(생성자 주입)을 임의의 코드 없이 자동으로 설정.

**@RequiredArgsConstructor 적용 전**

```java
@Component
public class Member {

    private final MemberService memberService;
    private final String id;

    @Autowired
    public Member(MemberService memberService, String id) {
        this.memberService = memberService;
        this.id = id;
    }

// ...생략

}
```
<br/> 

**@RequiredArgsConstructor 적용 후**

```java
@Component
@RequiredArgsConstructor
public class Member {

    private final MemberService memberService;
    private final String id;

// ...생략

}
```



<br/>
<br/> 

## @NoArgsConstructor

- 파라미터가 없는 생성자를 생성한다.
- 사용 시 주의 사항
    - 필드들이 final로 생성되어 있는 경우에는 필드를 초기화할 수 없기 때문에 생성자를 만들 수 없고 에러가 발생한다. → @NoArgsConstructor(force=true) 옵션을 이용해 final 필드를 강제 초기화 시켜 생성자를 만들 수 있다.
    - @Nonnull 같이 필드에 제약조건이 설정되어 있는 경우, 추후 초기화를 진행하기 전까지 생성자 내 null-check 로직이 생성되지 않는다.