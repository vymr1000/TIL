### Object 클래스의 clone() 메소드

Object 클래스가 가지는 `clone()` 메소드는 기본적으로 다음과 같은 기능을 가진다.

- Java 클래스 객체의 복사본을 만든다.
- 대상 객체로부터 같은 속성값을 가진 복사본을 생성한다.
- 복제할 객체는 클래스의 설계자가 복제를 허용한다는 의도를 나타내기 위해 `Cloneable` 인터페이스를 명시해야 한다.

`clone()` **메소드를 왜 사용하는가?**

`clone()` 을 통해 객체를 복사한다면 그 복사는 Shallow Copy일까 Deep Copy일까? 기본적으로 `super.clone()` 메소드 호출을 통해 객체를 복사하게 되면 원본 클래스에 정의된 모든 필드와 같은 값을 가지도록 그대로 복사된다.  primitive 변수의 값이 그대로, reference 변수가 가지는 주소값이 그대로 복사된다. 

```java
	
// 학생 객체를 선언한다
Student stnt = new Student();

// 학생의 수강과목 선언
List<String> subjects = new ArrayList<String>(Arrays.asList("Network", "Data Stucture"));

// super.clone 메소드를 통한 카피. shallow copy이다
Student copyOfStnt = stnt.clone();

// 두 객체의 단순 비교: 동일한 객체가 아니므로 false를 리턴한다
System.out.println(stnt == copyOfStnt);

// 객체가 가지는 필드의 값을 수정한다 - 원본 배열 수정이 발생한다 
copyOfStnt.getSubjects().add("OS");
System.out.println(Arrays.toString(stnt.getSubjects()));
System.out.println(Arrays.toString(copyOfStnt.getSubjects()));
System.out.println(System.identityHashCode(stnt.getSubjects()));
System.out.println(System.identityHashCode(copyOfStnt.getSubjects()));

/***********
출력결과
[Network, Data Stucture, OS]
[Network, Data Stucture, OS]
747464370
747464370
-> 원본 데이터(창성동) 수정이 일어남. 같은 배열을 참조한다 (문제가 되는 부분)
-> 값을 가리키는 포인터를 복사하는 것이기 때문이다 (Shallow Copy)
 ************/

```

<br/>

### 복사 생성자

복사 생성자는 같은 클래스의 다른 객체를 사용하여 객체를 복사하는 생성자이다. 여러 필드가 있는 복잡한 개체를 복사하거나 기존 개체의 전체 복사본을 만들 때 유용하다. `Cloneable` 및 `CloneNotSupportedException` 이 사라지기 때문에 인터페이스 구현 및 예외 처리를 고려하지 않아도 된다는 장점이 있다.

```java
	
public class Student {
    private String studentId;
    private List<String> subjects;

    // 생성자
    public Student(String studentId, List<String> subjects) {
    this.studentId = studentId;
    this.subjects = subjects;
    }

    // 복사 생성자
    public Student(Student student) {
    this.studentId = student.studentId;
    this.subjects = new ArrayList<>(student.subjects);
    }
}
```

```java
// Main.java

// subject 배열 선언
List<String> subjects = new ArrayList<String>(Arrays.asList("Network", "Data Stucture"));

// 학생 객체 생성
Student stnt = new Student("123", subjects);

// 복사 생성자를 통한 복사
Student copyConstructor = new Student(stnt);

// subject 배열 선언
List<String> subjects = new ArrayList<String>(Arrays.asList("Network", "Data Stucture"));

// 학생 객체 생성
Student stnt = new Student("123", subjects);

// 복사 생성자를 통한 복사
Student copyConstructor = new Student(stnt);

copyConstructor.getSubjects().add("OS");
System.out.println(stnt.getSubjects());
System.out.println(copyConstructor.getSubjects());
System.out.println(System.identityHashCode(stnt.getSubjects()));
System.out.println(System.identityHashCode(copyConstructor.getSubjects()));

/***********
출력결과
[Network, Data Stucture]
[Network, Data Stucture, OS]
1018547642
1456208737
-> 원본 데이터에 영향가지 않는다. (Deep Copy) 
************/
```