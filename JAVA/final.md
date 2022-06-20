### final

final 키워드는 기본적으로 한번 할당이 되면 그 이후 변수에 대해 변경이 불가하게 만드는 키워드이다. final 키워드를 사용할 수 있는 곳은 4군데로 정리할 수 있다.

1. 지역변수
2. 클래스 멤버변수
3. 메소드 매개변수
4. 클래스와 메소드 

### **멤벼변수와 final 키워드**

```java
public class SoftwareDept {
		public final int MAX_PEOPLE = 50;
		... 이하 생략
}
```

클래스 멤버변수에 final 키워드를 붙이게 되면 멤버변수의 변경을 막을 수 있다.

### 메소드 매개변수에서의 final 키워드

```java
public int add(final int num1, final int num2) {
		num1 += 1; // 컴파일 오류
}
```

매개변수에 final 키워드를 붙이게 되면 매개변수를 전달받은 메서드 안에서 인자 값 변경을 막을 수 있다. 

### 클래스와 메소드

- final 클래스 는 상속할 수 없는 클래스를 만든다.
- final 메소드는 오버라이딩할 수 없게 된다.

### final와 불변의 관계

final는 불변을 보장하는 키워드일까? 다음과 같은 예제 클래스를 정의해보았다. 주문클래스를 만들고 그 안에 멤버 필드를 정의하여 final 키워드를 통해 주문데이터에 대한 유저데이터의 불변을 보장하려고 한다.

```java
// 주문 클래스
public class Order {
    private final String orderNum;  // 주문번호
		// 주문유저를 의미하는 Member클래스를 정의하고 final 키워드를 사용힌다.
    private final Member member; 
    private List<String> orderItems =  new ArrayList<>();  // 주문상품

    public Order(String orderNum, Member member, List<String> orderItems) {
        this.orderNum = orderNum;
        this.member = member;
        this.orderItems = orderItems;
    }

    public String getOrderNum() {
        return orderNum;
    }

    public Member getMember() {
        return member;
    }

    public List<String> getOrderItems() {
        return orderItems;
    }
}

// Main 클래스
public class Main {
    public static void main(String[] args) {

        // 주문유저
        Member member = new Member(110, "Mike");
        List<String> items = new ArrayList<>();
        items.add(0, "table");
        items.add(1, "knife");
        
        // 주문생성
        Order order = new Order("K20220620", member, items);
        // 주문 생성후 주문유저의 name 필드를 출력한다.
        System.out.println(order.getMember().getName());
        
        // 주문유저의 이름을 변경해본다.
        member.setName("Andrew");
        System.out.println(order.getMember().getName());

    }
}

/************
출력결과
Mike
Andrew
************/
```

어찌보면 당연한 결과겠지만 주문유저의 필드가 변경이 되었다. Order 클래스에서 Member 클래스를 final 키워드로 선언하였지만 확인된것처럼  **final 키워드는 불변을 보장하지 않는다.** 이처럼 클래스 멤버변수에서 final 키워드를 사용하고 싶을때 참조필드에 대한 주의를 해야한다. final 키워드는 한번 할당된 데이터에 대해 재할당을 방지하는 키워드이지 데이터 불변 자체를 보장하지 않기 때문이다.