## Test Driven Development(테스트 주도 개발, TDD)
### 용어 정리
Production Code
```
public class Calculator {

    int add(int i, int j) {
        return i + j;
    }

    int subtract(int i, int j) {
        return i - j;
    }

    int multiply(int i, int j) {
        return i * j;
    }

    int divide(int i, int j) {
        return i / j;
    }
}
```
* 위 코드를 통해 확인할 수 있듯이 **프로덕션 코드(Production Code)**는 프로그램 구현을 담당하는 부분으로 사용자가 실제로 사용하는 소스 코드를 의미합니다.

Test Code
```
public static void main(String[] args) {
    Calculator cal = new Calculator();
    System.out.println(cal.add(3, 4));
    System.out.println(cal.subtract(5, 4));
    System.out.println(cal.multiply(2, 6));
    System.out.println(cal.divide(8, 4));
}
```
* 테스트 코드(test code)는 프로덕션 코드가 정상적으로 동작하는지를 확인하는 코드입니다.

### TDD란?
* TDD = TFD(Test First Development) + 리팩토링
* TDD란 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고 이를 제어하는 기술입니다.
* TDD의 아이러니 중 하나는 테스트 기술이 아니라는 점입니다. TDD는 분석 기술이며, 설계 기술이기도 합니다.

### TDD를 하는 이유
* 디버깅 시간을 줄여줍니다.
* 동작하는 문서 역할을 합니다.
* 변화에 대한 두려움을 줄여줍니다.

### TDD 사이클
![1]()   
* 실패하는 테스트를 구현합니다.
* 테스트가 성공하도록 프로덕션 코드를 구현합니다.
* 프로덕션 코드와 테스트 코드를 리팩토링합니다.

### TDD 원칙
* 원칙 1 - 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드(production code)를 작성하지 않습니다.
* 원칙 2 - 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성합니다.
* 원치 3 - 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성합니다.

## 참조
* [우아한테크캠프 Pro 3기](https://edu.nextstep.camp/)