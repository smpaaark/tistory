정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 게 아닙니다. 하지만 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어줍니다.

```추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없습니다.``` 하위 클래스를 만들어 인스턴스화하면 그만입니다.

## 인스턴스화를 막는 방법
컴파일러가 기본 생성자를 만드는 경우는 오직 명시된 생성자가 없을 때뿐이니 ```private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있습니다.```
```
package com.smpaaark.item4;

// 코드 4-1 인스턴스를 만들 수 없는 유틸리티 클래스 (26~27쪽)
public class UtilityClass {

    // 기본 생성자가 만들어지는 것을 막는다(인스턴스화 방지용).
    private UtilityClass() {
        throw new AssertionError();
    }

    // 나머지 코드는 생략
}
```
(UtilityClass.java)
* 인스턴스를 만들 수 없는 유틸리티 클래스
* 명시적 생성자가 private이니 클래스 바깥에서는 접근할 수 없습니다.
* 이 방식은 상속을 불가능하게 하는 효과도 있습니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)