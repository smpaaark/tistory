## equals 메서드 재정의가 필요 없는 경우
다음에서 열거한 상황 중 하나에 해당한다면 equals 메서드를 재정의하지 않는 것이 최선입니다.
* ```각 인스턴스가 본질적으로 고유합니다.``` 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 여기 해당합니다. Thread가 좋은 예로, Object의 equals 메서드는 이러한 클래스에 딱 맞게 구현되었습니다.
* ```인스턴스의 '논리적 동치성(logical equality)'을 검사할 일이 없습니다.``` 예컨대 java.util.regex.Pattern은 equals를 재정의해서 두 Pattern의 인스턴스가 같은 정규표현식을 나타내는지를 검사하는, 즉 논리적 동치성을 검사하는 방법도 있습니다. 하지만 설계자는 클라이언트가 이 방식을 원하지 않거나 애초에 필요하지 않다고 판단할 수도 있습니다. 설계자가 후자로 판단했다면 Object의 기본 equals만으로 해결됩니다.
* ```상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞습니다.``` 예컨대 대부분의 Set 구현체는 AbstractSet이 구현한 equals를 상속받아 쓰고, List 구현체들은 AbstractList로부터, Map 구현체들은 AbstractMap으로부터 상속받아 그대로 씁니다.
* ```클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없습니다.``` 여러분이 위험을 철저히 회피하는 스타일이라 equals가 실수로라도 호출되는 걸 막고 싶다면 다음처럼 구현해두면 됩니다.
```
@Override public boolean equals(Object o) {
    throw new AssertionError(); // 호출 금지!
}
```

## equals 메서드를 재정의해야 하는 경우
객체 식별성(object identity; 두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때입니다. 주로 Integer와 String처럼 값을 표현하는 값 클래스들이 여기 해당됩니다.

값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 인스턴스 통제 클래스라면 equals를 재정의하지 않아도 됩니다. Enum도 여기에 해당합니다.

## equals 메서드를 재정의할 때 따라야할 일반 규약
다음은 Object 명세에 적힌 규약입니다.
> equals 메서드는 동치관계(equivalence relation)를 구현하며, 다음을 만족합니다.
> * ```반사성(reflexivity)```: null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true입니다.
> * ```대칭성(symmetry)```: null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)가 true면 y.equals(x)도 true입니다.
> * ```추이성(transitivity)```: null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y)가 true이고 y.equals(z)도 true면 x.equals(z)도 true입니다.
> * ```일관성(consistency)```: null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환합니다.
> * ```null-아님```: null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false입니다.   

컬렉션 클래스들을 포함해 수많은 클래스는 전달받은 객체가 equals 규약을 지킨다고 가정하고 동작합니다.

### 동치관계
쉽게 말해, 집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연산입니다. 이 부분집합을 동치류(equivalence class; 동치 클래스)라 합니다.

### 반사성
단순히 말하면 객체는 자기 자신과 같아야 한다는 뜻입니다. 이 요건을 어긴 클래스의 인스턴스를 컬렉션에 넣은 다음 contains 메서드를 호출하면 방금 넣은 인스턴스가 없다고 답할 것입니다.

### 대칭성
두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다는 뜻입니다.
```
package com.smpaaark.item10;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

// 코드 10-1 잘못된 코드 - 대칭성 위배! (54-55쪽)
public final class CaseInsensitiveString {
    
    private final String s;

    public CaseInsensitiveString(String s) {
        this.s = Objects.requireNonNull(s);
    }

    // 대칭성 위배!
    @Override public boolean equals(Object o) {
        if (o instanceof CaseInsensitiveString)
            return s.equalsIgnoreCase(
                    ((CaseInsensitiveString) o).s);
        if (o instanceof String)  // 한 방향으로만 작동한다!
            return s.equalsIgnoreCase((String) o);
        return false;
    }

    ...

}
```
(CaseInsensitiveString.java)
* 잘못된 코드 - 대칭성 위배

```
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
```
* cis.equals(s)는 true를 반환합니다.
* 문제는 CaseInsensitiveString의 equals는 일반 String을 알고 있지만 String의 equals는 CaseInsensitiveString의 존재를 모른다는 데 있습니다.
* 따라서 s.equals(cis)는 false를 반환하여, 대칭성을 명백히 위반합니다.

```
List<CaseInsensitiveString> list = new ArrayList<>();
list.add(cis);
```
* list.contains(s)를 호출한 결과는 현재는 false가 나오지만 순전히 구현하기 나름이라 OpenJDK 버전이 바뀌거나 다른 JDK에서는 true를 반환하거나 런타임 예외를 던질 수도 있습니다.
* ```equals 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알 수 없습니다.```

```
@Override public boolean equals(Object o) {
    return o instanceof CaseInsensitiveString &&
            ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
}
```
* CaseInsensitiveString의 equals를 String과 연동하지 못하게 변경해야 합니다.

### 추이성
첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다는 뜻입니다.
```
package com.smpaaark.item10;

// 단순한 불변 2차원 정수 점(point) 클래스 (56쪽)
public class Point {
    
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;
        Point p = (Point)o;
        return p.x == x && p.y == y;
    }

    ...

}
```
* 2차원에서의 점을 표현하는 클래스

```
package com.smpaaark.item10.inheritance;

import com.smpaaark.item10.Color;
import com.smpaaark.item10.Point;

// Point에 값 컴포넌트(color)를 추가 (56쪽)
public class ColorPoint extends Point {

    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }

    ...

}
```
* Point 클래스를 확장해서 점에 색상을 더한 클래스
* equals 메서드를 그대로 둔다면 Point의 구현이 상속되어 색상 정보는 무시한 채 비교를 수행합니다.

```
@Override public boolean equals(Object o) {
    if (!(o instanceof ColorPoint))
        return false;
    return super.equals(o) && ((ColorPoint) o).color == color;
}
```
* 비교 대상이 또 다른 ColorPoint이고 위치와 색상이 같을 때만 true를 반환하는 equals 메서드
* 잘못된 코드 - 대칭성 위배
* Point를 ColorPoint와 비교환 결과와 그 둘을 바꿔 비교한 결과가 다를 수 있습니다.
* Point의 equals는 색상을 무시하고, ColorPoint의 equals는 입력 매개변수의 클래스 종류가 다르다며 매번 false만 반환할 것입니다.

```
Point p = new Point(1, 2);
ColorPoint cp = new ColorPoint(1, 2, Color.RED);
```
* p.equals(cp)는 true를, cp.equals(p)는 false를 반환합니다.

```
@Override public boolean equals(Object o) {
    if (!(o instanceof Point))
        return false;

    // o가 일반 Point면 색상을 무시하고 비교한다.
    if (!(o instanceof ColorPoint))
        return o.equals(this);

    // o가 ColorPoint면 색상까지 비교한다.
    return super.equals(o) && ((ColorPoint) o).color == color;
}
```
* ColorPoint.equals가 Point와 비교할 때 색상을 무시하도록 변경
* 잘못된 코드 - 대칭성 위배
* 이 방식은 대칭성은 지켜주지만, 추이성을 깨버립니다.

```
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2);
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);
```
* p1.equals(p2)와 p2.equals(p3)는 true를 반환하는데, p1.equals(p3)가 false를 반환합니다.

이 방식은 무한 재귀에 빠질 위험도 있습니다. Point의 또 다른 하위 클래스로 SmellPoint를 만들고, equals는 같은 방식으로 구현했다고 했을 때, myColorPoint.equals(mySmellPoint)를 호출하면 StackOverflowError를 일으킵니다.

```구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않습니다.```
```
@Override public boolean equals(Object o) {
    if (o == null || o.getClass() != getClass())
        return false;
    Point p = (Point) o;
    return p.x == x && p.y == y;
}
```
* 잘못된 코드 - 리스코프 치환 원칙 위배
* 이번 equals는 같은 구현 클래스의 객체와 비교할 때만 true를 반환합니다.
* Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 합니다.

```
// 단위 원 안의 모든 점을 포함하도록 unitCircle을 초기화한다. (58쪽)
private static final Set<Point> unitCircle = Set.of(
        new Point( 1,  0), new Point( 0,  1),
        new Point(-1,  0), new Point( 0, -1));

public static boolean onUnitCircle(Point p) {
    return unitCircle.contains(p);
}
```
* 주어진 점이 (반지름이 1인) 단위 원 안에 있는지를 판별하는 메서드

```
package com.smpaaark.item10.inheritance;

import com.smpaaark.item10.Point;

import java.util.concurrent.atomic.*;

// Point의 평범한 하위 클래스 - 값 컴포넌트를 추가하지 않았다. (59쪽)
public class CounterPoint extends Point {

    private static final AtomicInteger counter =
            new AtomicInteger();

    public CounterPoint(int x, int y) {
        super(x, y);
        counter.incrementAndGet();
    }
    public static int numberCreated() { return counter.get(); }

}
```
(CounterPoint.java)
* 값을 추가하지 않는 방식으로 Point를 확장한 클래스
* 리스코프 치환 원칙(Liskov substitution principle)에 따르면, 어떤 타입에 있어 중요한 속성이라면 그 하위 타입에서도 마찬가지로 중요합니다.

Point 클래스의 equals를 getClass를 사용해 작성했다면 CounterPoint의 인스턴스를 onUnitCircle 메서드에 넘겼을 때 onUnitCircle은 false를 반환할 것입니다. Set을 포함하여 대부분의 컬렉션은 주어진 원소를 담고 있는지를 확인하는 작업에 equals 메서드를 이용하는데, CounterPoint의 인스턴스는 어떤 Point와도 같을 수 없습니다. 반면, Point의 equals를 instanceof 기반으로 올바로 구현했다면 CounterPoint 인스턴스를 건네줘도 onUnitCircle 메서드가 제대로 동작할 것입니다.

구체 클래스의 하위 클래스에서 값을 추가할 방법은 없지만 괜찮은 우회 방법이 하나 있습니다. "상속 대신 컴포지션을 사용하라"는 아이템 18의조언을 따르면 됩니다. Point를 상속하는 대신 Point를 ColorPoint의 private 필드로 두고, ColorPoint와 같은 위치의 일반 Point를 반환하는 뷰(View) 메서드를 public으로 추가하는 식입니다.
```
package com.smpaaark.item10.composition;

import com.smpaaark.item10.Color;
import com.smpaaark.item10.Point;

import java.util.Objects;

// 코드 10-5 equals 규약을 지키면서 값 추가하기 (60쪽)
public class ColorPoint {

    private final Point point;
    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        point = new Point(x, y);
        this.color = Objects.requireNonNull(color);
    }

    /**
     * 이 ColorPoint의 Point 뷰를 반환한다.
     */
    public Point asPoint() {
        return point;
    }

    @Override public boolean equals(Object o) {
        if (!(o instanceof ColorPoint))
            return false;
        ColorPoint cp = (ColorPoint) o;
        return cp.point.equals(point) && cp.color.equals(color);
    }

    ...

}
```
(ColorPoint.java)
* equals 규약을 지키면서 값 추가하기

자바 라이브러리에도 구체 클래스를 확장해 값을 추가한 클래스가 종종 있습니다.
* java.sql.Timestamp는 java.util.Data를 확장한 후 nanoseconds 필드를 추가했습니다.
* 그 결과로 Timestamp의 equals는 대칭성을 위배하며, Data 객체와 한 컬렉션에 넣거나 서로 섞어 사용하면 엉뚱하게 동작할 수 있습니다.

> 추상 클래스의 하위 클래스에서라면 equals 규약을 지키면서도 값을 추가할 수 있습니다. 상위 클래스를 직접 인스턴스로 만드는게 불가능하다면 지금까지 이야기한 문제들은 일어나지 않습니다.

### 일관성
두 객체가 같다면 (어느 하나 혹은 두 객체 모두가 수정되지 않는 한) 앞으로도 영원히 같아야 한다는 뜻입니다. 가변 객체는 비교 시점에 따라 서로 다를 수도 혹은 같을 수도 있는 반면, 불변 객체는 한번 다르면 끝까지 달라야 합니다.

클래스가 불변이든 가변이든 ```equals의 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안 됩니다.``` 예컨대 java.net.URL의 equals는 주어진 URL과 매핑된 호스트의 IP 주소를 이용해 비교합니다. 호스트 이름을 IP 주소로 바꾸려면 네트워크를 통해야 하는데, 그 결과가 항상 같다고 보장할 수 없습니다. 이런 문제를 피하려면 equals는 항시 메모리에 존재하는 객체만을 사용한 결정적(deterministic) 계산만 수행해야 합니다.

### null-아님
이름처럼 모든 객체가 null과 같지 않아야 한다는 뜻입니다.
```
// 명시적 null 검사 - 필요 없다!
@Override public boolean equals(Object o) {
    if (o == null)
        return false;

    ...
}
```
* 수많은 클래스가 다음 코드 처럼 입력이 null인지를 확인해 자신을 보호합니다.
* 이러한 검사는 필요치 않습니다.
* 동치성을 검사하려면 equals는 건네받은 객체를 적절히 형변환한 후 필수 필드들의 값을 알아내야 합니다.
* 그러려면 형변환에 앞서 instanceof 연산자로 입력 매개변수가 올바른 타입인지 검사해야 합니다.

```
// 묵시적 null 검사 - 이쪽이 낫다.
@Override public boolean equals(Object o) {
    if (!(o instanceof MyType)))
        return false;

    MyType mt = (MyType) o;
    ...
}
```
* equals가 타입을 확인하지 않으면 잘못된 타입이 인수로 주어졌을 때 ClassCastException을 던져서 일반 규약을 위배하게 됩니다.
* instanceof는 입력이 null이면 타입 확인 단계에서 false를 반환하기 때문에 null 검사를 명시적으로 하지 않아도 됩니다.

## 양질의 equals 메서드 구현 방법
1. ```== 연산자를 사용해 입력이 자기 자신의 참조인지 확인합니다.``` 자기 자신이면 true를 반환합니다. 이는 단순한 성능 최적화용으로, 비교 작업이 복잡한 상황일 때 값어치를 할 것입니다.
2. ```instanceof 연산자로 입력이 올바른 타입인지 확인합니다.``` 그렇지 않다면 false를 반환합니다. 이때의 올바른 타입은 equals가 정의된 클래스인 것이 보통이지만, 가끔은 그 클래스가 구현한 특정 인터페이스가 될 수도 있습니다. 어떤 인터페이스는 자신을 구현한 (서로 다른) 클래스끼리도 비교할 수 있도록 equals 규약을 수정하기도 합니다. 이런 인터페이스를 구현한 클래스라면 equals에서 (클래스가 아닌) 해당 인터페이스를 사용해야 합니다. Set, List, Map, Map.Entry 등의 컬렉션 인터페이스들이 여기 해당합니다.
3. ```입력을 올바른 타입으로 형변환합니다.``` 앞서 2번에서 instanceof 검사를 했기 때문에 이 단계는 100% 성공합니다.
4. ```입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사합니다.``` 모든 필드가 일치하면 true를, 하나라도 다르면 false를 반환합니다. 2단계에서 인터페이스를 사용했다면 입력의 필드 값을 가져올 때도 그 인터페이스의 메서드를 사용해야 합니다. 타입이 클래스라면 (접근 권한에 따라) 해당 필드에 직접 접근할 수도 있습니다.

### 필드 타입별 비교 방법
float와 double을 제외한 기본 타입 필드는 == 연산자로 비교하고, 참조 타입 필드는 각각의 equals 메서드로, float와 double 필드는 각각 정적 메서드인 Float.compare(float, float)와 Double.compare(double, double)로 비교합니다. 배열 필드는 원소 각각을 앞서의 지침대로 비교합니다. 배열의 모든 원소가 핵심 필드라면 Arrays.equals 메서드들 중 하나를 사용합니다.

때론 null도 정상 값으로 취급하는 참조 타입 필드도 있습니다. 이런 필드는 정적 메서드인 Objects.equals(Object, Object)로 비교해 NullPointerException 발생을 예방해야 합니다.

비교하기가 아주 복잡한 필드를 가진 클래스도 있습니다. 이럴 때는 그 필드의 표준형(canonical form)을 저장해둔 후 표준형끼리 비교하면 훨씬 경제적입니다. 이 기법은 불변 클래스에 제격입니다.

### 필드 비교 순서
어떤 필드를 먼저 비교하느냐가 equals의 성능을 좌우하기도 합니다. 최상의 성능을 바란다면 다를 가능성이 더 크거나 비교하는 비용이 싼 (혹은 둘 다 해당하는) 필드를 먼저 비교합니다. 동기화용 락(lock) 필드 같이 객체의 논리적 상태와 관련 없는 필드는 비교하면 안됩니다. 핵심 필드로부터 계산해낼 수 있는 파생 필드 역시 굳이 비교할 필요는 없지만, 파생 필드를 비교하는 쪽이 더 빠를 때도 있습니다. 

## 단위 테스트
```equals를 다 구현했다면 세 가지만 자문해봅니다. 대칭적인가? 추이성이 있는가? 일괄적인가?``` 자문에서 끝내지 말고 단위 테스트를 작성해 돌려봅니다. 단, equals 메서드를 AutoValue를 이용해 작성했다면 테스트를 생략해도 안심할 수 있습니다. 
```
package com.smpaaark.item10;

// 코드 10-6 전형적인 equals 메서드의 예 (64쪽)
public final class PhoneNumber {

    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix   = rangeCheck(prefix,   999, "프리픽스");
        this.lineNum  = rangeCheck(lineNum, 9999, "가입자 번호");
    }

    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max)
            throw new IllegalArgumentException(arg + ": " + val);
        return (short) val;
    }

    @Override public boolean equals(Object o) {
        if (o == this)
            return true;
        if (!(o instanceof PhoneNumber))
            return false;
        PhoneNumber pn = (PhoneNumber)o;
        return pn.lineNum == lineNum && pn.prefix == prefix
                && pn.areaCode == areaCode;
    }

    // 나머지 코드는 생략 - hashCode 메서드는 꼭 필요하다(아이템 11)!

}
```
(PhoneNumber.java)
* 전형적인 equals 메서드의 예

## 주의사항
* ```equals를 재정의할 땐 hashCode도 반드시 재정의합니다.```
* ```너무 복잡하게 해결하려 들지 않습니다.``` 필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있습니다. 오히려 너무 공격적으로 파고들다가 문제를 일으키기도 합니다. 일반적으로 별칭(alias)은 비교하지 않는게 좋습니다. 예컨대 File 클래스라면, 심볼릭 링크를 비교해 같은 파일을 가리키는지를 확인하려 들면 안 됩니다. 다행히 File 클래스는 이런 시도를 하지 않습니다.
* Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하면 안됩니다. 많은 프로그래머가 equals를 다음과 같이 작성해놓고 문제의 원인을 찾아 헤맵니다.
```
// 잘못된 예 - 입력 타입은 반드시 Object여야 한다!
public boolean equals(MyClass o) {
    ...
}
```
* 이 메서드는 입력 타입이 Object가 아니므로 재정의가 아니라 다중정의한 것입니다.
* 이 메서드는 하위 클래스에서의 @Override 애너테이션이 긍정 오류(false poitive; 거짓 양성)를 내게 하고 보안 측면에서도 잘못된 정보를 줍니다.
* @Override 애너테이션을 일관되게 사용하면 컴파일되지 않고, 무엇이 문제인지를 정확히 알려주는 오류 메시지를 보여줄 것입니다.
```
// 여전히 잘못된 예 - 컴파일되지 않음
@Override public boolean equals(MyClass o) {
    ...
}
```

## AutoValue 프레임워크
equals(hashCode도 마찬가지) 테스트를 대신해줄 구글이 만든 오픈소스입니다. 클래스에 애너테이션 하나만 추가하면 AutoValue가 이 메서드들을 알아서 작성해주며, 여러분이 직접 작성하는 것과 근본적으로 똑같은 코드를 만들어줄 것입니다.

## 핵심 정리
> 꼭 필요한 경우가 아니라면 equals를 재정의하지 말아야합니다. 많은 경우에 Object의 equals가 여러분이 원하는 비교를 정확히 수행해줍니다. 재정의해야 할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯 가지 규약을 확실히 지켜가며 비교해야 합니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)