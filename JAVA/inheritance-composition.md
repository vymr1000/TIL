### 상속(is-a / Inheritance) & 컴포지션(has-a / Composition)

객체지향 프로그래밍의 장점중 하나는 코드 재사용이라고 말할 수 있다. 상속(is-a 관계) 또는 컴포지션(has-a 관계)을 구현하여 코드 재사용을 수행할 수 있다. 객체지향 프로그래밍에서 is-a의 개념은 완전히 상속 기반이며, 클래스 상속 또는 인터페이스 상속의 두 가지 유형이 될 수 있다. 그것은 마치 "A is a B type of thing"이라고 말하는 것과 같다. is-a 관계는 “Apple is a Fruit”, “Car is a Vehicle” 문장이 성립하는 관계라고 말할 수 있다. 컴포지션(has-a)은 단순히 다른 객체에 대한 참조인 인스턴스 변수의 사용을 의미한다. 예를 들어 “Mercedes has Engine”, “House has Bathroom” 같은 문장이 예시가 된다.

**상속의 문제점: 캡슐화 약화, 개방폐쇄원칙**

상속받은 클래스는 상위 클래스의 `public`, `protected` 메소드에 접근이 가능하기 때문에 상위 클래스의 **구현사항에 강하게 의존하게 된다**. 이는 캡슐화가 깨진다는 것을 의미하는데, 이렇다는 것은 상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있음을 말한다. 상위 클래스의 구현이 달라짐에 따라 그 여파로 코드 한 줄 건드리지 않은 하위 클래스가 오동작할 수 있다. 하위 클래스 설계원칙에 부합하지 않는 메소드가 상위클래스에 존재한다면, 그 메소드 또한 하위 클래스로 상속되기 때문에 문제가 발생한다. 완벽한 캡슐화를 원한다면 상속을 포기하거나 적절한 `private` 사용으로 인터페이스 은닉과 함께 `getter`, `setter` 코드 구현 등 정확하게 상속을 구현해내야 한다.

**상속의 문제점: 설계가 유연하지 않다, 클래스간 결합 문제**

상속을 흔히 **확장성**에 유리하다고 알려져있다. ****이것은 상속이 하위 클래스에 기능을 점진적으로 추가하며 확장하는 목적으로는 편리하다는 것을 의미한다. 그러나 상속받은 하위 클래스는 상위 클래스와 상호 간의 의존성(결합도, coupling)이 생긴다. 객체지향 프로그래밍에서는 클래스 간 결합도를 낮추고 해당 클래스가 갖는 책임에 집중(응집도, cohesion)하여 독립성을 높이는 방향성을 추구한다.

**잘못된 상속 예 1: Stack 클래스**

상속을 단순히 코드 재사용과 다형성을 목적으로 사용해서는 안된다. 상속에 대한 잘못된 예는 Stack 클래스의 예로 확인해볼 수 있다.

`Stack` 클래스의 경우 `Vector` 클래스를 상속받는다. 따라서 `Vector` 클래스의 `public`/`protected` 인터페이스는 모두 접근이 가능함을 의미한다. `Vector` 클래스의 `add` 메소드는 `public`으로 정의되어 있어 `Stack` 클래스 안에서 사용할 수 있다. 그런데 `add` 메소드를 `Stack` 클래스에서 호출하면 `Stack` 클래스 규칙에 맞지 않는 결과가 반환될 수 있다.

```java
Stack<String> stk = new Stack<>();
stk.push("1st");
stk.push("2nd");
stk.push("3rd");
stk.add(0, "4th");
 
assertEquals("4th", stk.pop()); // 에러
```

위 코드의 경우 Stack 클래스의 원소를 더하는 연산은 항상 FILO를 준수해야 한다. 허나 add 메소드로 넣은 원소는 가장 맨 앞에 위치하므로 pop연산 시 LIFO 원칙에 위배된다. 또한 Vector 클래스의 get 메소드도 Stack에서 사용할 수 있어 원소를 확인하고자 하면 pop연산으로 값을 확인해야 함에도 get 메소드를 사용하여 조회할 수 있어 역시 Stack 규칙에 어긋난다.

**잘못된 상속 예 2: HashSet 클래스**

아래 `InstrumentedHashSet`클래스의 `addAll` 메소드를 통해 원소를 추가하고 `getAddCount`을 통해 원소의 크기를 나타내는 변수를 출력해보자. 

```java
// HashSet로부터 상속받은 InstrumentedHashSet 클래스 정의
public class InstrumentedHashSet<E> extends HashSet<E> {
		// 추가된 원소의 수
		private int addCount = 0;
		
		public InstrumentedHashSet() {
		}
		
		public InstrumentedHashSet(int initCap, float loadFactor) {
				super(initCap, loadFactor);
		}

		@Override
		public boolean add(E e) {
				addCount++;
				System.out.println("add = " + addCount);
				return super.add(e);
		}
		
		@Override
		public boolean addAll(Collection< ? extends E> c) {
				addCount += c.size();
				System.out.println("addAll = " + addCount);
				return super.addAll(c);
		}

		public int getAddCount() {
				return addCount();
		}
		
}
```

자바 List.asList로 리스트를 생성하여 아래와 같이 메소드를 호출한다.

```java
public static void main(String[] args) {
      CustomHashSet<String> customHashSet = new CustomHashSet<>();
      List<String> test = Arrays.asList("알파", "베타", "감가");
      customHashSet.addAll(test);

      System.out.println(customHashSet.getAddCount());
}

/*********** 출력 결과 ***********
addAll = 3
add = 4
add = 5
add = 6
6
*******************************/
```

이제 `getAddCount` 메소드를 호출하면 3을 리턴하는 것을 기대하겠지만 실제로는 6을 리턴한다. 그 원인은 `HashSet(상위클래스)`의 `addAll`메소드가 `add` 메소드를 이용해 구현된 데 있다. `InstrumentedHashSet(자식클래스)` 의 `addAll`은 `addCount`에 3을 더한후 `HashSet`의 `addAll`을 호출했다. `HashSet` 의 `addAll`은 각 원소를 `add`메소드를 호출해 추가하는데, 이때 불리는 `add` 는 `InstrumentedHashSet` 에서 재정의한 메소드이다. 따라서 중복되게 카운트되고 6이 리턴된다.

**잘못된 상속문제를 해결하는 방법: 컴포지션 (Composition)**

위에서 발생하는 문제들을 모두 피해가는 방법이 있다. 기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 `private` 필드로 기존 클래스의 인스턴스를 참조하게 하자. 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션이라고 한다.