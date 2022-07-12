## Anonymoous Class

익명 클래스(Anonymous class)를 사용하면 코드를 더 간결하게 만들 수 있다. 클래스를 선언하면서 동시에 인스턴스화할 수 있기 때문이다. 이름이 없다는 점을 제외하고는 기본적으로는 로컬 클래스와 동일하다. 클래스를 굳이 따로 정의할 필요없는 경우. 즉, 로컬 클래스를 한 번만 사용해야 하는 경우 익명클래스로 사용하면 좋다.

<br/>

### 익명클래스의 선언

익명클래스를 선언하는 방법은 무엇일까. 여기서 알아야할 차이점은 로컬 클래스는 **클래스 선언(declaration)**이지만 익명 클래스는 **표현식(expresstion)이라는 것**이다. 즉, 기존과는 다른 방식으로 클래스를 정의해야 한다. 다음 예제 `HelloWorldAnonymousClasses`는 지역 변수 `FrenchGreeting` 및 `spanishGreeting`의 초기화 문에서 익명 클래스를 사용하지만 변수 `englishGreeting`의 초기화에는 로컬 클래스를 사용한다.

```java
public class HelloWorldAnonymousClasses {
  
    interface HelloWorld {
        public void greet();
        public void greetSomeone(String someone);
    }
  
    public void sayHello() {
        
				// 로컬 클래스 선언
        class EnglishGreeting implements HelloWorld {
            String name = "world";
            public void greet() {
                greetSomeone("world");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hello " + name);
            }
        }
      
				// 클래스 객체 생성
        HelloWorld englishGreeting = new EnglishGreeting();
        
				// 익명클래스를 통한 객체 할당, 선언과 동시 할당
        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };

				// 익명클래스를 통한 객체 할당, 선언과 동시 할당
        HelloWorld spanishGreeting = new HelloWorld() {
            String name = "mundo";
            public void greet() {
                greetSomeone("mundo");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hola, " + name);
            }
        };
        englishGreeting.greet();
        frenchGreeting.greetSomeone("Fred");
        spanishGreeting.greet();
    }

    public static void main(String... args) {
        HelloWorldAnonymousClasses myApp =
            new HelloWorldAnonymousClasses();
        myApp.sayHello();
    }            
}
```

<br/>

### 익명클래스의 구문

익명 클래스는 **표현식(expresstion)**이다. 익명 클래스 표현식의 구문은 코드 블록에 클래스 정의가 포함되어 있다는 점을 제외하고 **생성자의 호출**과 유사하다. 위 예제의 `frenchGreeting` 선언부를 주목해보자.

```java
// 익명클래스의 선언, 생성자 호출방식과 유사하다
HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };
```

익명 클래스 표현식은 다음으로 구성된다.

- new 연산자
- 구현할 인터페이스 또는 상속받을 클래스의 이름. 이 예제에서 익명 클래스는 `HelloWorld` 인터페이스를 구현하고 있다.
- 일반 클래스 객체 생성 표현과 마찬가지로 생성자에 대한 인수를 포함하는 괄호. 인터페이스를 구현할 때 생성자가 없으므로 이 예제와 같이 빈 괄호 쌍을 사용한다.
- 클래스 선언 본문인 본문. 본문에서 메소드 선언은 허용되지만 [명령문(statement)](#statement)은 허용되지 않는다.
    
    익명 클래스 정의는 표현식이므로 명령문의 일부여야 한다. 이 예제에서 익명 클래스 표현식은 `FrenchGreeting` 객체를 인스턴스화하는 문의 일부이기 때문에 본문에 또다른 명령문이 들어갈 수 없다.
    
<br/>

### 익명클래스의 특징

익명 클래스에서 다음을 선언할 수 있다.

- 멤버변수
- 메소드
- 인스턴스 초기화
- 로컬 클래스

그러나 익명 클래스에서는 생성자를 선언할 수 없다.

이 외에 익명클래스는 아래와 같은 특징을 가지고 있다.

- 익명 클래스는 final로 선언되지 않은 클래스 선언부 밖 범위의 지역 변수에 액세스할 수 없다.
- **중첩 클래스(nested class)**와 마찬가지로 익명 클래스의 형식 선언은 동일한 이름을 가진 바깥쪽 범위의 다른 선언을 숨깁니다. **이름만으로 숨겨진 선언을 참조할 수 없다.**
    
    ```java
    public class ShadowTest {
    
        public int x = 0;
    
        class FirstLevel {
    
            public int x = 1;
    
            void methodInFirstLevel(int x) {
                System.out.println("x = " + x);
                System.out.println("this.x = " + this.x);
                System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);
            }
        }
    
        public static void main(String... args) {
            ShadowTest st = new ShadowTest();
            ShadowTest.FirstLevel fl = st.new FirstLevel();
            fl.methodInFirstLevel(23);
        }
    }
    
    /**** 출력결과 *****
    23
    1
    0
    ******************/
    ```
    
    x라는 변수의 이름만으로 멤버변수 x를 참조할 수 없다. this 키워드로 x 할당 범위를 명시한다.
    

<br/>
<br/>

### statement

Java 공식문서에서 언급되는 명령문(statement)은 완전한 실행 단위를 말한다. 아래와 같은 구문이다.

- 값 할당 (Assignment)
- 단항연산자 `++` or `--`
- 메소드 호출
- 객체 생성 표현식

```java
// 값 할당 (Assignment)
aValue = 8933.234;
// 단항연산자 ++ or -- 
aValue++;
// 메소드 호출
System.out.println("Hello World!");
// 객체 생성 표현식
Bicycle myBike = new Bicycle();
```