## 불변 클래스
불변 클래스란 간단히 말해 그 인스턴스의 내부 값을 수정할 수 없는 클래스입니다. String, 기본 타입의 박싱된 클래스들, BigInteger, BigDecimal이 여기 속합니다. 불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전합니다.

## 불변 클래스를 만드는 다섯 가지 규칙
* ```객체의 상태를 변경하는 메서드(변경자)를 제공하지 않습니다.```
* ```클래스를 확장할 수 없도록 합니다.``` 하위 클래스에서 부주의하게 혹은 나쁜 의도로 객체의 상태를 변하게 만드는 사태를 막아줍니다. 상속을 막는 대표적인 방법은 클래스를 final로 선언하는 것이지만, 다른 방법도 뒤에 살펴볼 것입니다.
* ```모든 필드를 final로 선언합니다.``` 시스템이 강제하는 수단을 이용해 설계자의 의도를 명확히 드러내는 방법입니다. 새로 생성된 인스턴스를 동기화 없이 다른 스레드로 건네도 문제없이 동작하게끔 보장하는 데도 필요합니다.
* ```모든 필드를 private으로 선언합니다.``` 필드가 참조하는 가변 객체를 클라이언트에서 직접 접근해 수정하는 일을 막아줍니다. 기술적으로는 기본 타입 필드나 불변 객체를 참조하는 필드를 public final로만 선언해도 불변 객체가 되지만, 이렇게 하면 다음 릴리스에서 내부 표현을 바꾸지 못하므로 권하지는 않습니다.
* ```자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 합니다.``` 클래스에 가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야 합니다. 이런 필드는 절대 클라이언트가 제공한 객체 참조를 가리키게 해서는 안되며, 접근자 메서드가 그 필드를 그대로 반환해서도 안 됩니다. 생성자, 접근자, readObject 메서드 모두에서 방어적 복사를 수행해야 합니다.

## 예시
```
public final class Complex {

    private final double re;
    private final double im;

    public double realPart()      { return re; }
    public double imaginaryPart() { return im; }

    public Complex plus(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }

    public Complex minus(Complex c) {
        return new Complex(re - c.re, im - c.im);
    }

    public Complex times(Complex c) {
        return new Complex(re * c.re - im * c.im,
                re * c.im + im * c.re);
    }

    public Complex dividedBy(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp,
                (im * c.re - re * c.im) / tmp);
    }

    @Override public boolean equals(Object o) {
        if (o == this)
            return true;
        if (!(o instanceof Complex))
            return false;
        Complex c = (Complex) o;

        // == 대신 compare를 사용하는 이유는 63쪽을 확인하라.
        return Double.compare(c.re, re) == 0
                && Double.compare(c.im, im) == 0;
    }

    @Override public int hashCode() {
        return 31 * Double.hashCode(re) + Double.hashCode(im);
    }

    @Override public String toString() {
        return "(" + re + " + " + im + "i)";
    }

}
```
* 불변 복소수 클래스
* 사칙연산 메서드들이 인스턴스 자신은 수정하지 않고 새로운 Complex 인스턴스를 만들어 반환하는 모습에 주목해야 합니다.
* 이처럼 피연산자에 함수를 적용해 그 결과를 반환하지만, 피연산자 자체는 그대로인 프로그래밍 패턴을 함수형 프로그래밍이라 합니다.
* 이와 달리, 절차적 혹은 명령형 프로그래밍에서는 피연산자인 자신을 수정해 자신의 상태가 변하게 됩니다.

## 불변 객체의 장점
### 단순함
```불변 객체는 단순합니다.``` 불변 객체는 생성된 시점의 상태를 파괴될 때까지 그대로 간직합니다. 반면 가변 객체는 변경자 메서드가 일으키는 상태 전이를 정밀하게 문서로 남겨놓지 않으면 믿고 사용하기 어렵습니다.

### 스레드 안전
```불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없습니다.``` 여러 스레드가 동시에 사용해도 절대 훼손되지 않습니다. 불변 객체에 대해서는 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니 ```불변 객체는 안심하고 공유할 수 있습니다.``` 따라서 불변 클래스라면 한번 만든 인스턴스를 최대한 재활용하기를 권합니다. 가장 쉬운 재활용 방법은 자주 쓰이는 값들을 상수(public static final)로 제공하는 것입니다.
```
public static final Complex ZERO = new Complex(0, 0);
public static final Complex ONE  = new Complex(1, 0);
public static final Complex I    = new Complex(0, 1);
```
* 더 나아가 불변 클래스는 자주 사용되는 인스턴스를 캐싱하여 같은 인스턴스를 중복 생성하지 않게 해주는 정적 팩터리를 제공할 수 있습니다.
* 이런 정적 팩터리를 사용하면 여러 클라이언트가 인스턴스를 공유하여 메모리 사용량과 가비지 컬렉션 비용이 줄어듭니다.
* 새로운 클래스를 설계할 때 public 생성자 대신 정적 팩터리를 만들어두면, 클라이언트를 수정하지 않고도 필요에 따라 캐시 기능을 나중에 덧붙일 수 있습니다.

### 방어적 복사 필요 없음
불변 객체를 자유롭게 공유할 수 있다는 점은 방어적 복사도 필요 없다는 결론으로 자연스럽게 이어집니다. 그러니 불변 클래스는 clone 메서드나 복사 생성자를 제공하지 않는게 좋습니다.

### 내부 데이터 공유
```불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있습니다.``` negate 메서드를 통해 생성한 BigInteger 인스턴스의 배열은 비록 가변이지만 복사하지 않고 원본 인스턴스와 공유해도 됩니다. 그 결과 새로 만든 BigInteger 인스턴스도 원본 인스턴스가 가리키는 내부 배열을 그대로 가리킵니다.

### 불변 객체를 구성요소로 사용
```객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많습니다.``` 값이 바뀌지 않는 구성요소들로 이뤄진 객체라면 그 구조가 아무리 복잡하더라도 불변식을 유지하기 훨씬 수월하기 때문입니다.

### 실패 원자성 제공
```불변 객체는 그 자체로 실패 원자성을 제공합니다.``` 상태가 절대 변하지 않으니 잠깐이라도 불일치 상태에 빠질 가능성이 없습니다.
> 실패 원자성(failure atomicity): '메서드에서 예외가 발생한 후에도 그 객체는 여전히 (메서드 호출 전과 똑같은) 유효한 상태여야 한다'는 성질입니다.

## 불변 객체의 단점
```값이 다르면 반드시 독립된 객체로 만들어야 합니다.``` 값의 가짓수가 많다면 이들을 모두 만드는 데 큰 비용을 치러야 합니다.
```
BigInteger moby = ...;
moby = moby.flipBit(0)
```
* flipBit 메서드는 새로운 BigInteger 인스턴스를 생성합니다.
* 원본과 단지 한 비트만 다른 백만 비트짜리 인스턴스를 생성합니다.
* 이 연산은 BigInteger의 크기에 비례해 시간과 공간을 잡아먹습니다.

BitSet도 BigInteger처럼 임의 길이의 비트 순열을 표현하지만, BigInteger와는 달리 '가변'입니다. BigSet 클래스는 원하는 비트 하나만 상수 시간 안에 바꿔주는 메서드를 제공합니다.
```
BigSet moby = ...;
moby.flip(0);
```

## 성능 문제 대처 방법
원하는 객체를 완성하기까지의 단계가 많고, 그 중간 단계에서 만들어진 객체들이 모두 버려진다면 성능 문제가 더 불거집니다.

### 1. 흔히 쓰일 다단계 연산(mutistep operation)들을 예측하여 기본 기능으로 제공하는 방법
이러한 다단계 연산을 기본으로 제공한다면 더 이상 각 단계마다 객체를 생성하지 않아도 됩니다. 불변 객체는 내부적으로 아주 영리한 방식으로 구현할 수 있기 때문입니다.

### 2. public 가변 동반 클래스 제공
클라이언트들이 원하는 복잡한 연산들을 정확히 예측할 수 있다면 pacakge-private의 가변 동반 클래스만으로 충분합니다. 그렇지 않다면 이 클래스를 public으로 제공하는 게 최선입니다. 자바 플랫폼 라이브러리에서 이에 해당하는 대표적인 예가 String 클래스과 가변 동반 클래스인 StringBuilder입니다.

## 불변 클래스를 만드는 또 다른 설계 방법
자신을 상속하지 못하게 하는 가장 쉬운 방법은 final 클래스로 선언하는 것이지만, 더 유연한 방법이 있습니다. 모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공하는 방법입니다.
```
public final class Complex {

    private final double re;
    private final double im;

    private Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }

    // 코드 17-2 정적 팩터리(private 생성자와 함께 사용해야 한다.) (110-111쪽)
    public static Complex valueOf(double re, double im) {
        return new Complex(re, im);
    }

    ...

}
```
* 생성자 대신 정적 팩터리를 사용한 불변 클래스
* 이 방식이 최선일 때가 많습니다.
* 바깥에서 볼 수 없는 package-private 구현 클래스를 원하는 만큼 만들어 활용할 수 있으니 훨씬 유연합니다.
* 패키지 바깥의 클라이언트에서 바라본 이 불변 객체는 사실상 final입니다.
* public이나 protected 생성자가 없으니 다른 패키지에서는 이 클래스를 확장하는 게 불가능하기 때문입니다.

## BigInteger와 BigDecimal
BigInteger와 BigDecimal을 설계할 당시엔 불변 객체가 사실상 final이어야한다는 생각이 널리 퍼지지 않았습니다. 그래서 이 두 클래스의 메서드들은 모두 재정의할 수 있게 설계되었고, 안타깝게도 하위 호환성이 발목을 잡아 지금까지도 이 문제를 고치지 못했습니다. 신뢰할 수 없는 하위 클래스의 인스턴스라고 확인되면, 이 인수들은 가변이라 가정하고 방어적으로 복사해 사용해야 합니다.
```
public static BigInteger safeInstance(BigInteger val) {
    return val.getClass() == BigInteger.class ? val : new BigInteger(val.toByteArray());
}
```

## 불변 클래스 규칙 완화
불변 클래스의 규칙에서 모든 필드가 final이고 어떤 메서드도 그 객체를 수정할 수 없어야 한다고 했는데, 이 규칙은 좀 과한 감이 있어서, 성능을 위해 다음처럼 살짝 완화할 수 있습니다. "어떤 메서드도 객체의 상태 중 외부에 비치는 값을 변경할 수 없습니다." 어떤 불변 클래스는 계산 비용이 큰 값을 나중에 (처음 쓰일 때) 계산하여 final이 아닌 필드에 캐시해놓기도 합니다. 이 묘수는 순전히 그 객체가 불변이기 때문에 부릴 수 있는데, 몇 번을 계산해도 항상 같은 결과가 만들어짐이 보장되기 때문입니다.

> 직렬화할 때는 추가로 주의할 점이 있습니다. Serializable을 구현하는 불변 클래스의 내부에 가변 객체를 참조하는 필드가 있다면 readObject나 readResolve 메서드를 반드시 제공하거나, ObjectOutputStream.writeUnshared와 ObjectInputStream.readUnshared 메서드를 사용해야 합니다. 플랫폼이 제공하는 기본 직렬화 방법이면 충분하더라도 말입니다. 그렇지 않으면 공격자가 이 클래스로부터 가변 인스턴스를 만들어낼 수 있습니다.

## 정리
게터(Getter)가 있다고 해서 무조건 세터(setter)를 만들지 말아야 합니다. ```클래스는 꼭 필요하 경우가 아니라면 불변이어야 합니다.``` 불변 클래스는 장점이 많으며, 단점이라곤 특정 상황에서의 잠재적 성능 저하뿐입니다. 단순한 값 객체는 항상 불변으로 만들어야 합니다(자바 플랫폼에서도 원래는 불변이어야 했지만 그렇지 않게 만들어진 객체가 몇 개 있습니다. java.util.Date와 java.awt.Point가 그렇습니다). 성능 때문에 어쩔 수 없다면 불변 클래스와 쌍을 이루는 가변 동반 클래스를 public 클래스로 제공하도록 합시다.

## 불변으로 만들 수 없는 경우
```불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄여야 합니다.``` 객체가 가질 수 있는 상태의 수를 줄이면 그 객체를 예측하기 쉬워지고 오류가 생길 가능성이 줄어듭니다. 그러니 꼭 변경해야 할 필드를 뺀 나머지 모두를 final로 선언해야 합니다. 결론은 ```다른 합당한 이유가 없다면 모든 필드는 private final이어야 합니다.```

## 생성자
```생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 합니다.``` 확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공해서는 안 됩니다. 

## CountDowunLatch 클래스
java.util.concurrent 패키지의 CountDownLatch 클래스가 이상의 원칙을 잘 방증합니다. 비록 가변 클래스지만 가질 수 있는 상태의 수가 많지 않습니다. 카운트가 0에 도달하면 더는 재사용할 수 없습니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)