### Object 클래스의 clone() 메소드

Object 클래스가 가지는 `clone()` 메소드는 기본적으로 다음과 같은 기능을 가진다.

- Java 클래스 객체의 복사본을 만든다.
- 대상 객체로부터 같은 속성값을 가진 복사본을 생성한다.
- 복제할 객체는 클래스의 설계자가 복제를 허용한다는 의도를 나타내기 위해 `Cloneable` 인터페이스를 명시해야 한다.

<br/>

`clone()` **메소드를 왜 사용하는가?**   
`clone()` 을 통해 객체를 복사한다면 그 복사는 Shallow Copy일까 Deep Copy일까?  기본적으로 `super.clone()` 메소드 호출을 통해 객체를 복사하게 되면 원본 클래스에 정의된 모든 필드와 같은 값을 가지도록 그대로 복사된다. 객체를 복사해서 사용할 때는 가변 객체의 원본은 보존하면서 변경을 가하고 싶은 경우에 사용하는 것이 일반적이다. Student 클래스를 직접 정의하고 그 안에 필드를 수정해보자. 

<br/>

```java
// Student.java
public class Student implements Cloneable {
    private String[] address;
    private List<String> subjects = new ArrayList<>();
    ... 이하 생략
}


// Main.java
public class Main {
    public static void main(String[] args) {

        Student stnt = new Student();

        String[] address = {"서울특별시", "종로구", "창성동"};
        String[] address2 = {"서울특별시", "용산구", "문배동"};

        // stnt 객체 address 데이터 세팅.
        stnt.setAddress(address);
        // stnt 객체 subjects 데이터 세팅.
        stnt.getSubjects().add("Network");
        stnt.getSubjects().add("Data Stucture");

        // clone 메소드를 통한 카피.
        Student copyOfStnt = stnt.clone();

        // TEST: clone 객체 address 데이터 세팅 후 출력 1.
        copyOfStnt.getAddress()[2] = "계동";
        System.out.println(Arrays.toString(stnt.getAddress()));
        System.out.println(Arrays.toString(copyOfStnt.getAddress()));
        /***********
        출력결과
        [서울특별시, 종로구, 계동]
        [서울특별시, 종로구, 계동]
        -> 원본 데이터(창성동) 수정이 일어남.
        ************/

        // TEST: clone 객체 address 데이터 세팅 후 출력 2.
        copyOfStnt.setAddress(address2);
        System.out.println(Arrays.toString(stnt.getAddress()));
        System.out.println(Arrays.toString(copyOfStnt.getAddress()));
        /***********
        출력결과
        [서울특별시, 종로구, 계동]
        [서울특별시, 용산구, 문배동]
        -> 원본 데이터 수정이 일어나지 않음.
        ************/

        copyOfStnt.getSubjects().add("OS");
        System.out.println(stnt.getSubjects());
        System.out.println(copyOfStnt.getSubjects());
        /***********
        출력결과
        [Network, Data Stucture, OS]
        [Network, Data Stucture, OS]
        -> 원본 데이터 수정이 일어남.
        ************/

        System.out.println(System.identityHashCode(stnt));
        System.out.println(System.identityHashCode(copyOfStnt));
        /***********
        출력결과
        747464370
        1018547642
        -> 객체의 메모리 주소값이 다름.
        ************/

        System.out.println(System.identityHashCode(stnt.getAddress()));
        System.out.println(System.identityHashCode(copyOfStnt.getAddress()));
        /***********
        출력결과
        312116338
        453211571
        -> 객체의 멤버 변수 address 메모리 주소가 다름.
        ************/

        System.out.println(System.identityHashCode(stnt.getSubjects()));
        System.out.println(System.identityHashCode(copyOfStnt.getSubjects()));
        /***********
        출력결과
        1456208737
        1456208737
        -> 객체의 멤버 변수 subjects 메모리 주소가 같음.
        ************/

        System.out.println(stnt == copyOfStnt);
        /***********
        출력결과
        false
        ************/
}
```