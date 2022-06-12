# String


Java에서 String은 불변(Immutable) 객체로 정의된다. 불변 객체란 객체가 생성된 후 상태가 변하지 않고 계속 유지되는 객체를 말한다. String 인스턴스는 한 번 생성되면 그 값을 읽기만 할 수 있고, 변경할 수는 없다.

```java
String str1 = "Coffee"; // String Literal
str1 = "Coke"; 
String str2 = "Coke"; // String Literal
String str3 = new String("Coke"); // String Object
```

<img src="./images/string-heap.jpg" width="500px"/>

위 예시에서 `str1`과 `str2`는, `"Coke"`라는 value를 갖는 String Pool 내부의 하나의 String 객체를 바라보고 있다. 이 때 str1의 값을 `"Coffee"`로 바꾼다면 `str1`은 String Pool 내부의 다른 String 객체를 바라보게 된다.

<br/>

Java의 String은 불변성(immutability) 이라는 특성이 있어서 JVM은 String 객체에 대한 메모리 할당을 최적화 할 수 있다. 메모리 내부에 String pool 이라는 공간을 만들어 두고 고유한 리터럴 문자열을 저장하여 이를 참조하여 사용하도록 하는 방법을 사용해서 말이다. 이 과정을 **String Interning** 이라고 한다. 기본적으로 String 변수를 생성하고 문자열을 할당 (리터럴) 하게 되면 JVM 내부에서는 문자열 값이 String Pool 에 존재하는지 확인한다. 이때 발견이 된다면 Java 컴파일러는 해당 문자열 리터럴의 주소에 대한 참조를 반환하고 찾지 못한다면 해당 문자열의 리터럴을 Pool에 저장한 후에 참조를 반환한다.

<br/>

왜 String은 불변성을 가지게 설계되었을까. Java에서 String을 불변으로 만든 이유로는 크게 3가지가 있다.

1. 메모리 최적화
2. 성능 최적화 (해시코드 캐싱)
3. Thread Safe

<br/><br/>

### 메모리 할당 최적화

String은 가장 많이 사용되는 데이터 타입 중 하나이다. 그렇다는 것은 Java 어플리케이션에서 String 타입의 객체들이 가장 많은 메모리를 차지한다는 말이다. Java 언어를 설계할 당시 문자열 객체는 재사용 될 가능성이 높기때문에 **같은 값일 경우** 어플리케이션당 하나의 String 객체만을 생성해두어 JVM의 힙(heap)을 절약하고자 했던 것이다.

<br/><br/>

### 성능 최적화 (해시코드 캐싱)

해시맵, 해시테이블, 해시셋 등과 같은 자료구조에서 해시 구현에 따라 작동할 때 `hashCode()` 메서드가 꽤 자주 호출된다. 앞서 설명한 자료구조들은 key의 hash를 통해 value를 저장하는 구조를 가지고 있다. 따라서 매번 `hashCode()` 메서드를 호출하게 된다. 이 때, 매번 HashCode를 계산하게 된다면 성능에 영향을 줄 수 있다. 이런 부분을 고려하여 Java에서 String은 hash 값을 계산한 적이 없을 때 최초 1번만 실제 계산을 실행하고, 이 후 해당 값을 그냥 반환만 하도록 하여 재활용 할 수 있도록 설계되어 있다. 이런 설계가 가능한 이유는 String이 독립적이고 불변이기 때문이라고 할 수 있다.

<br/><br/>

### Thread Safe

객체가 불변이면 멀티 스레드 환경에서도 값이 바뀔 위험이 없기 때문에, 자연스럽게 Thread Safe한 특성을 갖게 되고, 동기화와 관련된 위험 요소에서 벗어날 수 있다. 여러 스레드에서 동시에 접근해도 별다른 문제가 없다.또한 String의 경우 한 스레드에서 값을 바꾸면, 해당 객체의 값을 수정하는 것이 아니라 새로운 객체를 String Pool에 생성한다. 따라서 Thread Safe하다고 볼 수 있다.