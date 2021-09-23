## 퇴보한 클래스
인스턴스 필드들을 모아놓는 일 외에는 아무 목적도 없는 퇴보한 클래스
```
class Point {

    public double x;
    public double y;

}
```
* 이처럼 퇴보한 클래스는 public이어서는 안 됩니다.
* 이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못합니다.
* API를 수정하지 않고는 내부 표현을 바꿀 수 없고, 불변식을 보장할 수 없으며, 외부에서 필드에 접근할 때 부수 작업을 수행할 수도 없습니다.

## 캡슐화 적용한 클래스
철저한 객체 지향 프로그래머는 이런 클래스를 상당히 싫어해서 필드들을 모두 private으로 바꾸고 public 접근자(getter)를 추가합니다.
```
class Point {

    private double x;
    private double y;

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }

    public double getY() { return y; }

    public void setX(double x) { this.x = x; }

    public void setY(double y) { this.y = y; }

}
```
* 접근자와 변경자(mutator) 메서드를 활용해 데이터를 캡슐화합니다.
* public 클래스라면 이 방식이 확실히 맞습니다.
* ```패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공```함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있습니다.

## package-private 혹은 private 중첩 클래스
```package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없습니다.``` 이 방식은 클래스 선언 면에서나 이를 사용하는 클라이언트 코드 면에서나 접근자 방식보다 훨씬 깔끔합니다. 클라이언트 코드가 이 클래스 내부 표현에 묶이기는 하나, 클라이언트도 어차피 이 클래스를 포함하는 패키지 안에서만 동작하는 코드일 뿐입니다.

## Point와 Demension 클래스
자바 플랫폼 라이브러리에도 public 클래스의 필드를 직접 노출하지 말라는 규칙을 어기는 사례가 종종 있는데, 대표적인 예가 java.awt.package 패키지의 Point와 Dimension 클래스입니다.

## 불변 필드
public 클래스의 필드가 불변이라면 직접 노출할 때의 단점이 조금은 줄어들지만, 여전히 결코 좋은 생각이 아닙니다. API를 변경하지 않고는 표현 방식을 바꿀 수 없고, 필드를 읽을 때 부수 작업을 수행할 수 없다는 단점은 여전합니다. 단, 불변식은 보장할 수 있게 됩니다. 다음 클래스는 각 인스턴스가 유효한 시간을 표현함을 보장합니다.
```
public final class Time {

    private static final int HOURS_PER_DAY    = 24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("Hour: " + hour);
        if (minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("Min: " + minute);
        this.hour = hour;
        this.minute = minute;
    }

    // 나머지 코드 생략
}
```
* 불변 필드를 노출한 public 클래스

## 핵심 정리
> public 클래스는 절대 가변 필드를 직접 노출해서는 안 됩니다. 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없습니다. 하지만 package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있습니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)