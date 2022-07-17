### 배열을 복사하려면 어떻게 해야될까 


**핵심 → 배열의 요소가 참조형일 경우에는 각 원소에 대해 전부 clone을 해준다**
배열을 복사하려면 어떻게 해야될까. shallow copy 문제와 더불어 배열을 복사하기 위한 방법을 살펴본다.

<br/>

다음과 같은 Fruit 클래스가 있다.

```java
public class Fruit {
    private String name;
    private int price;
    public Fruit(String name, int price) {
        this.name = name;
        this.price = price;
    }
		@Override
		    public String toString() {
		        return "{ " + this.name + ": " + this.price + " }";
		    }
}
```

```java
ArrayList<Fruit> fruits = new ArrayList<>();
fruits.add(new Fruit("Apple", 3500));
fruits.add(new Fruit("Orange", 7500));

// fruit 배열 출력해보기
System.out.println(fruits); 
/**** 출력결과 ****

[{ Apple: 3500 }, { Orange: 7500 }]

*****************/

// fruit 배열을 clone 해본다 -> shallow copy일까?
ArrayList<Fruit> copyOfFruits = (ArrayList<Fruit>)fruits.clone();
System.out.println(System.identityHashCode(fruits));
System.out.println(System.identityHashCode(copyOfFruits));
/**** 출력결과 *****

114132791
586617651

--> 다른 주소값을 가진다, 메모리 안 배열의 시작 위치는 다름을 의미한다
*****************/

// 각 원소의 주소값을 출력해보자
for(Fruit e: fruits) {
    System.out.println(System.identityHashCode(e));
}

for(Fruit e: copyOfFruits) {
    System.out.println(System.identityHashCode(e));
}

/**** 출력결과 *****

328638398
1789550256
328638398
1789550256

--> 각 원소가 같은 주소값을 가진다
--> 배열의 원소가 같은 객체를 참조하고 있으며 이는 shallow copy임을 의미한다
*****************/
```

`fruits`, `copyOfFruits` 은 결국 같은 원소를 가진 것이나 다름없다. 각 원소가 같은 주소값을 가지기 때문이다. `fruits`에 `add` 메소드로 원소를 추가했을 때는 `fruits`에만 원소가 추가되겠지만, `fruits`의 원소를 `setter`함수로 데이터 변경을 했을 때에는 `fruits`, `copyOfFruits` 모두 동일하게 변경될 것이다. 하지만 배열을 복사한다는 것은 원본 데이터를 그대로 보존하고 복사한 데이터를 가지고 변형할 것임을 의미할 것이다. 말그대로 deep copy가 필요한데, 그렇다면 각 원소에 대해 모두 clone을 통해 복사해주면 된다. 그러기 위해선 `Fruit` 클래스를 아래와 같이 수정해준다.

<br/>

```java
// Cloneable 인터페이스 구현 추가
public class Fruit implements Cloneable {
    private String name;
    private int price;
    public Fruit(String name, int price) {
        this.name = name;
        this.price = price;
    }
    @Override
    public String toString() {
        return "{ " + this.name + ": " + this.price + " }";
    }

    public void setPrice(int price) {
        this.price = price;
    }

		// clone 메소드 재정의
    @Override
    protected Fruit clone() throws CloneNotSupportedException {
        return (Fruit) super.clone();
    }
}
```

`copyOfFruits` 리스트에 각 원소를 clone 하여 add 해보자

```java
ArrayList<Fruit> fruits = new ArrayList<>();
fruits.add(new Fruit("Apple", 3500));
fruits.add(new Fruit("Orange", 7500));

ArrayList<Fruit> copyOfFruits = new ArrayList<>();
System.out.println(System.identityHashCode(fruits));
System.out.println(System.identityHashCode(copyOfFruits));

for(Fruit e: fruits) {
    System.out.println(System.identityHashCode(e));
    copyOfFruits.add(e.clone());
}

for(Fruit e: copyOfFruits) {
    System.out.println(System.identityHashCode(e));
}

/**** 출력결과 *****

328638398
1789550256
3447021
440434003

--> 각 원소가 다른 주소값을 가진다
--> 배열의 원소가 다른 객체를 참조하고 있으며 이는 deep copy임을 의미한다
*****************/
```