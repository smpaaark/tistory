## 의존성 추가
스프링 부트를 사용한다면 test 관련 의존성에 기본으로 추가되어 있습니다.   
스프링 부트를 사용하지 않는다면 아래 의존성을 추가해줍니다.
```
dependencies {
    testImplementation 'org.assertj:assertj-core:3.19.0'
    ...
}
```

이제부터는 성공하는 케이스만 작성되어 있으니 개별적으로 실패하는 케이스도 테스트 해보길 권장드립니다.

## contains
결과가 배열로 반환되는 경우 contains()를 활용해 기대하는 값이 배열에 포함되어 있는지 확인합니다.

### 예시 코드
```
@Test
@DisplayName("split 했을 때 1과 2로 잘 분리되는지 확인")
public void split_여러개() throws Exception {
    // given
    String input = "1,2";

    // when
    String[] splitArray = input.split(",");

    // then
    assertThat(splitArray).contains("1");
    assertThat(splitArray).contains("2");
}
```
### 결과
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5BJUnit%5D%20assertj%20%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/1.PNG)

## containsExactly
결과가 배열로 반환되는 경우 containsExactly()를 활용해 배열에 기대하는 값만 존재하는지 확인합니다.

### 예시 코드
```
@Test
@DisplayName("split 했을 때 1만을 포함하는 배열이 반환되는지 확인")
public void split_1개() throws Exception {
    // given
    String input = "1";

    // when
    String[] splitArray = input.split(",");

    // then
    assertThat(splitArray).containsExactly("1");
}
```

### 결과
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5BJUnit%5D%20assertj%20%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/2.PNG)

## isEqualTo
결과가 기대하는 값과 일치하는지 확인합니다.

### 예제 코드
```
@Test
@DisplayName("substring 기능 확인")
public void substring() throws Exception {
    // given
    String input = "(1,2)";

    // when
    String substring = input.substring(1, input.length() - 1);

    // then
    assertThat(substring).isEqualTo("1,2");
}
```

### 결과
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5BJUnit%5D%20assertj%20%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/3.PNG)

## assertThatThrownBy
익셉션이 발생하는 케이스를 테스트할 때 사용합니다.

### 예제 코드
```
@Test
@DisplayName("입력값 범위 밖일 경우 StringIndexOutOfBoundsException 확인")
public void charAt_범위_밖() throws Exception {
    // given
    String input = "abc";

    // when, then
    assertThatThrownBy(() -> input.charAt(input.length()))
            .isInstanceOf(StringIndexOutOfBoundsException.class)
            .hasMessageContaining("String index out of range")
            .hasMessageContaining(String.valueOf(input.length()));
}
```
* 파라미터: 익셉션이 발생하는 메서드를 람다식으로 입력
* 체이닝
  * isInstanceOf: 예상되는 익셉션을 .class 형태로 입력
  * hasMessageContaining: 익셉션 메시지에 입력되는 문자열이 포함되는지 확인

### 결과
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5BJUnit%5D%20assertj%20%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/4.PNG)

## isTrue
결과 값이 Ture인지 확인합니다.

### 예제 코드
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

### 결과
![5](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5BJUnit%5D%20assertj%20%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/5.PNG)
