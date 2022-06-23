## 체크 예외(Checked Exception)

체크 예외는 RuntimeException의 하위 클래스가 아니면서 Exception 클래스의 하위 클래스들입니다. `체크 예외의 특징은 반드시 에러 처리를 해야하는 특징(try/catch or throw)`을 가지고 있습니다.

- `존재하지 않는 파일의 이름을 입력(FileNotFoundException)`
- `실수로 클래스의 이름을 잘못 적음(ClassNotFoundException)`

## 언체크 예외(Unchecked Exception)

언체크 예외는 RuntimeException의 하위 클래스들을 의미합니다. 이것은 체크 예외와는 달리 에러 처리를 강제하지 않습니다.말 그대로 `실행 중에(runtime)` 발생할 수 있는 예외를 의미합니다.

- `배열의 범위를 벗어난(ArrayIndexOutOfBoundsException)`
- `값이 null이 참조변수를 참조(NullPointerException)`