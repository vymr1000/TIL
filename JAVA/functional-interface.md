## ****Functional Interface****

**함수형 인터페이스(Functional Interfaces)** 란 무엇일까. 함수형 인터페이스는 하나의 추상메소드를 가진 인터페이스를 의미한다. 이것은 람다식과 연결이 되는데, 함수형 인터페이스의 객체를 생성하는 방식으로 익명클래스를 활용하는 방식이 아닌 람다식으로도 생성할 수 있기 때문이다.

```java
// 인터페이스 선언
interface Calculate {
	int operation(int a, int b);
}
```

```java
// operation 메소드 호출을 위한 인터페이스 객체를 익명클래스로 구현
private void calculateClassic() {
	Calculate calculateAdd = new Calculate() {
		@Override
		public int operation(int a, int b) {
			return a+b;
		}
	};

	// operation 호출부
	System.out.println(calculateAdd.operation(1.2));
}
```

```java
// operation 메소드 호출을 위한 인터페이스 객체를 lambda로 구현 - 익명클래스보다 간결해진 구현이다
private void calculateClassic() {
	Calculate calculateAdd = (a, b) -> a+b;
	// operation 호출부
	System.out.println(calculateAdd.operation(1.2));
}
```
<br/>
<br/>

`Calculate` 라는 인터페이스는 일반적인 인터페이스 처럼 보이지만 이 인터페이스는 Functional(함수형) 인터페이스라고 부를 수 있다. 하지만 이 함수형 인터페이스에는 주의해야 할 점이 있다.

```java
// 메소드가 2개 있는 인터페이스 - 함수형 인터페이스가 될 수 없다.
interface Calculate {
	int operationAdd(int a, int b);
	int operationSubtract(int a, int b);
}
```

인터페이스에 이렇게 메소드가 두개 정의되어 있다면 람다식을 사용할 수 없고 컴파일 에러가 발생한다. 이런 혼동을 피하기 위해 `@FunctionalInterface` 어노테이션을 명시해준다.

```java
// @FunctionalInterface 어노테이션을 통해 이 인터페이스는 함수형 인터페이스임을 명시한다.
@FunctionalInterface
interface Calculate {
	int operation(int a, int b);
}
```

<br/>

### Reference
이상민, ⌜자바의 신 - 제2권⌟, 로드북(2017), p858-p861