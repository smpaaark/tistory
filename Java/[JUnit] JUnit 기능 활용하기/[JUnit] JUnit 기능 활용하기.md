## \@DisplayName
JUnit 테스트 결과 화면에 테스트 내용을 알아보기 쉽게 해주는 기능입니다.

### \@DisplayName를 사용하지 않은 경우
```
@Test
public void split_test() throws Exception {
    // given
    String input = "1,2";

    // when
    String[] splitArray = input.split(",");

    // then
    assertThat(splitArray).contains("1");
    assertThat(splitArray).contains("2");
}
```
![1]()   

### \@DisplayName를 사용한 경우
```
@Test
@DisplayName("split 했을 때 1과 2로 잘 분리되는지 확인")
public void split_test() throws Exception {
    // given
    String input = "1,2";

    // when
    String[] splitArray = input.split(",");

    // then
    assertThat(splitArray).contains("1");
    assertThat(splitArray).contains("2");
}
```
![2]()

## \@ParameterizedTest
중복 코드를 제거할 때 활용됩니다.   

아래 코드는 정상 작동하지만 중복되는 코드가 보입니다.
```
@Test
@DisplayName("contains() 메소드를 활용해 1, 2, 3의 값이 존재하는지 확인")
public void contains_확인() throws Exception {
    // given
    int num1 = 1;
    int num2 = 2;
    int num3 = 3;

    // when
    boolean result1 = numbers.contains(num1);
    boolean result2 = numbers.contains(num2);
    boolean result3 = numbers.contains(num3);

    // then
    assertThat(result1).isTrue();
    assertThat(result2).isTrue();
    assertThat(result3).isTrue();
}
```

\@ParameterizedTest를 아래와 같이 사용하면 중복 코드를 제거할 수 있습니다.
### 예시 코드1
```
@ParameterizedTest
@ValueSource(ints = {1, 2, 3})
@DisplayName("ParameterizedTest를 사용하여 contains 테스트")
public void ParameterizedTest_contains_확인(int inputNum) throws Exception {
    // given
    int num = inputNum;

    // when
    boolean result = numbers.contains(num);

    // then
    assertThat(result).isTrue();
}
```
* \@ValueSource: 테스트마다 순차적으로 입력되는 입력값입니다.
* 입력값이 메서드 매개변수인 inputNum으로 전달됩니다.

### 결과
![3]()   

### 예시 코드2
```
@ParameterizedTest
@CsvSource(value = {"1, true", "2, true", "3, true", "4, false", "5, false"})
@DisplayName("CsvSource 사용하여 contains 테스트")
public void CsvSource_contains_확인(int inputNum, boolean inputResult) throws Exception {
    // given
    int num = inputNum;

    // when
    boolean result = numbers.contains(num);

    // then
    assertThat(result).isEqualTo(inputResult);
}
```
* \@CsvSource: 테스트마다 순차적으로 입력되는 값이며, 콤마(,)를 기준으로 구분되서 메서드 매개변수로 전달됩니다.
* "1, true"를 예로 들면 콤마(,)를 기준으로 inputNum에는 1이 전달되고, inputResult에는 true가 전달됩니다.

### 결과
![4]()
