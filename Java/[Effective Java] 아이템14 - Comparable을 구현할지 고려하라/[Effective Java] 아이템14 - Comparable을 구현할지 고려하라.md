## compareTo 메서드
Comparable 인터페이스의 유일무이한 메서드입니다. compareTo는 Object의 메서드가 아닙니다. comapareTo는 단순 동치성 비교에 더해 순서까지 비교할 수 있으며, 제네릭합니다. Comparable을 구현했다는 것은 그 클래스의 인스턴스들에는 자연적인 순서(natural order)가 있음을 뜻합니다. 그래서 Compareble을 구현한 객체들의 배열은 다음처럼 손쉽게 정렬할 수 있습니다.
```
Arrays.sort(a);
```

다음 프로그램은 명령줄 인수들을 (중복은 제거하고) 알파벳순으로 출력합니다. String이 Comparable을 구현한 덕분입니다.
```
public class WordList {

    public static void main(String[] args) {
        Set<String> s = new TreeSet<>();
        Collections.addAll(s, args);
        System.out.println(s);
    }

}
```

사실상 자바 플랫폼 라이브러리의 모든 값 클래스와 열거타입이 Comparable을 구현했습니다. 알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면 반드시 Comparable 인터페이스를 구현해야합니다.
```
public interface Comparable<T> {

    public int compareTo(T o);

}
```

## compareTo 메서드의 일반 규약
equals의 규약과 비슷합니다.
> 이 객체와 주어진 객체의 순서를 비교합니다. 이 객체가 주어진 객체보다 작으면 음의 정수를, 같으면 0을, 크면 양의 정수를 반환합니다. 이 객체와 비교할 수 없는 타입의 객체가 주어지면 ClassCastException을 던집니다.
> 다음 설명에서 sgn(표현식) 표기는 수학에서 말하는 부호 함수(signum function)를 뜻하며, 표현식의 값이 음수, 0, 양수일 때 -1, 0, 1을 반환하도록 정의했습니다.
> * Comparable을 구현한 클래스는 모든 x, y에 대해 sgn(x.compareTo(y)) == -sgn(y.compareTo(x))여야 합니다(따라서 x.compareTo(y)는 y.compareTo(x)가 예외를 던질 때에 한해 예외를 던져야 합니다).
> * Comparable을 구현한 클래스는 추이성을 보장해야 합니다. 즉, (x.compareTo(y) > 0 && y.compareTo(z) > 0)이면 x.compareTo(z) > 0입니다.
> * Comparable을 구현한 클래스는 모든 z에 대해 x.compareTo(y) == 0 이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z))입니다.
> * 이번 권고가 필수는 아니지만 꼭 지키는게 좋습니다. (x.compareTo(y) == 0) == (x.equals(y))여야 합니다. Comparable을 구현하고 이 권고를 지키지 않는 모든 클래스는 그 사실을 명시해야 합니다. 다음과 같이 명시하면 적당할 것입니다.
> "주의: 이 클래스의 순서는 equals 메서드와 일관되지 않습니다."
* 모든 객체에 대해 전역 동치관계를 부여하는 equals 메서드와 달리, compareTo는 타입이 다른 객체를 신경 쓰지 않아도 됩니다.
* 타입이 다른 객체가 주어지면 간단히 ClassCastException을 던져도 되며, 대부분 그렇게 합니다.

hashCode 규약을 지키지 못하면 해시를 사용하는 클래스와 어울리지 못하듯, compareTo 규약을 지키지 못하면 비교를 활용하는 클래스와 어울리지 못합니다. 비교를 활용하는 클래스의 예로는 정렬된 컬렉션인 TreeSet과 TreeMap, 검색과 정렬 알고리즘을 활용하는 유틸리티 클래스인 Collections와 Arrays가 있습니다.

### 첫 번째 규약
두 객체 참조의 순서를 바꿔 비교해도 예상한 결과가 나와야 한다는 얘기입니다.

### 두 번째 규약
첫 번째가 두 번째보다 크고 두 번째가 세 번째보다 크면, 첫 번째는 세 번째보다 커야 한다는 뜻입니다.

### 세 번째 규약
크기가 같은 객체들끼리는 어떤 객체와 비교하더라도 항상 같아야 한다는 뜻입니다.

### 주의 사항
equals와 마찬가지로 기존 클래스를 확장한 구체 클래스에서 새로운 값 컴포넌트를 추가했다면 compareTo 규약을 지킬 방법이 없습니다. 우회법도 같습니다. Comparable을 구현한 클래스를 확장해 값 컴포넌트를 추가하고 싶다면, 확장하는 대신 독립된 클래스를 만들고, 이 클래스에 원래 클래스의 인스턴스를 가리키는 필드를 두면 됩니다. 그런 다음 내부 인스턴스를 반환하는 '뷰' 메서드를 제공하면 됩니다.

### 마지막 규약
compareTo의 마지막 규약은 필수는 아니지만 꼭 지키길 권합니다. 마지막 규약은 간단히 말하면 compareTo 메서드로 수행한 동치성 테스트의 결과가 equals와 같아야 한다는 것입니다. Collection, Set 혹은 Map과 같은 인터페이스들은 equals 메서드의 규약을 따른다고 되어 있지만, 놀랍게도 정렬된 컬렉션들은 동치성을 비교할 때 equals 대신 compareTo를 사용합니다.

### comapreTo와 equals가 일관되지 않는 예
빈 HashSet 인스턴스를 생성한 다음 new BigDecimal("1.0")과 new BigDecimal("1.00")을 차례로 추가합니다. 이 두 BigDecimal은 equals 메서드로 비교하면 서로 다르기 때문에 HashSet은 원소를 2개 갖게 됩니다. 하지만 HashSet 대신 TreeSet을 사용하면 원소를 하나만 갖게 됩니다. compareTo 메서드로 비교하면 두 BigDecimal 인스턴스가 똑같기 때문입니다.

## compareTo 메서드 작성 요령
Compareble은 타입을 인수로 받는 제네릭 인터페이스이므로 compareTo 메서드의 인수 타입은 컴파일타임에 정해집니다. 입력 인수의 타입을 확인하거나 형변환할 필요가 없다는 뜻입니다.

compareTo 메서드는 각 필드가 동치인지를 비교하는 게 아니라 그 순서를 비교합니다. 객체 참조 필드를 비교하려면 compareTo 메서드를 재귀적으로 호출합니다. Comparable을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 한다면 비교자(Comparator)를 대신 사용합니다.
```
public final class CaseInsensitiveString
        implements Comparable<CaseInsensitiveString> {

    ...

    // 자바가 제공하는 비교자를 사용해 클래스를 비교한다.
    public int compareTo(CaseInsensitiveString cis) {
        return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
    }

    ...

}
```
* 자바가 제공하는 비교자를 사용하고 있습니다.
* 객체 참조 필드가 하나뿐인 비교자
* implements Comparable<CaseInsensitiveString>
  * CaseInsensitiveString의 참조는 CaseInsensitiveString 참조와만 비교할 수 있다는 뜻으로, Comparable을 구현할 때 일반적으로 따르는 패턴입니다.

compareTo 메서드에서 정수 기본 타입 필드를 비교할 때 박싱된 기본 타입 클래스들에 새로 추가된 정적 메서드인 compare를 이용하면 됩니다. ```compareTo 메서드에서 관계 연산자 <와 >를 사용하는 이전 방식은 거추장스럽고 오류를 유발하니, 이제는 추천하지 않습니다.```

클래스에 핵심 필드가 여러 개라면 어느 것을 먼저 비교하느냐가 중요해집니다. 가장 핵심적인 필드부터 비교해나갑니다. 비교결과가 0이 아니라면, 즉 순서가 결정되면 거기서 끝입니다. 그 결과를 곧장 반환합니다.
```
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode);
    if (result == 0)  {
        result = Short.compare(prefix, pn.prefix);
        if (result == 0)
            result = Short.compare(lineNum, pn.lineNum);
    }
    return result;
}
```
* 기본 타입 필드가 여럿일 때의 비교자

자바 8에서는 Comparator 인터페이스가 일련의 비교자 생성 메서드(comparator construction method)와 팀을 꾸려 메서드 연쇄 방식으로 비교자를 생성할 수 있게 되었습니다. 많은 프로그래머가 이 방식의 간결함에 매혹되지만, 약간의 성능 저하가 뒤따릅니다. 
```
private static final Comparator<PhoneNumber> COMPARATOR =
        comparingInt((PhoneNumber pn) -> pn.areaCode)
                .thenComparingInt(pn -> pn.prefix)
                .thenComparingInt(pn -> pn.lineNum);

public int compareTo(PhoneNumber pn) {
    return COMPARATOR.compare(this, pn);
}
```
* 비교자 생성 메서드를 활용한 비교자
* 이 코드는 클래스를 초기화할 때 비교자 생성 메서드 2개를 이용해 비교자를 생성합니다.
* 그 첫 번째인 comparingInt는 객체 참조를 int 타입 키에 매핑하는 키 추출 함수(key extractor function)를 인수로 받아, 그 키를 기준으로 순서를 정하는 비교자를 반환하는 정적 메서드입니다.
* 앞의 예에서 comparingInt는 람다(lamda)를 인수로 받으며, 이 람다는 PhoneNumber에서 추출한 지역 코드를 기준으로 전화번호의 순서를 정하는 Comparator<PhoneNumber>를 반환합니다.
* 이 람다에서 입력 인수의 타입(PhoneNumber pn)을 명시한 점에 주목합니다.
* 자바의 타입 추론 능력이 이 상황에서 타입을 알아낼 만큼 강력하지 않기 때문에 프로그램이 컴파일되도록 도와준 것입니다.

두 전화번호의 지역 코드가 같을 수 있으니 비교 방식을 더 다듬어야 합니다. 이 일은 두 번째 비교자 생성 메서드인 thenComparingInt가 수행합니다. thenComparingInt는 Comparator의 인스턴스 메서드로, int 키 추출자 함수를 입력받아 다시 비교자를 반환합니다.(이 비교자는 첫 번째 비교자를 적용한 다음 새로 추출한 키로 추가 비교를 수행합니다). thenComparingInt는 원하는 만큼 연달아 호출할 수 있습니다. 이번에는 thenComparingInt를 호출할 때 타입을 명시하지 않았습니다. 자바의 타입 추론 능력이 이 정도는 추론해낼 수 있기 때문입니다.

## Comparator의 보조 생성 메서드들
Comparator는 long과 double용으로 compareInt와 thenComparingInt의 변형 메서드를 준비했습니다. short처럼 더 작은 정수 타입에는 (PhoneNumber 예에서처럼) int용 버전을 사용하면 됩니다. 마찬가지로 float은 double용을 이용해 수행합니다.

## Comparator의 객체 참조용 비교자 생성 메서드
우선, comparing이라는 정적 메서드 2개가 다중정의되어 있습니다. 첫 번째는 키 추출자를 받아서 그 키의 자연적 순서를 사용합니다. 두 번째는 키 추출자 하나와 추출된 키를 비교할 비교자까지 총 2개의 인수를 받습니다. 또한, thenComparing이란 인스턴스 메서드 3개가 다중정의되어 있습니다. 첫 번째는 비교자 하나만 인수로 받아 그 비교자로 부차 순서(comparing으로 1차 순서를 정하고, 이어지는 thenComparing으로 2차, 3차, ... 순서를 정함)를 정합니다. 두 번째는 키 추출자를 인수로 받아 그 키의 자연적 순서로 보조 순서를 정합니다. 마지막 세 번째는 키 추출자 하나와 추출된 키를 비교할 비교자까지 총 2개의 인수를 받습니다.

## 잘못된 방식
```
static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) {
        return o1.hashCode() - o2.hashCode();
    }
};
```
* 해시코드 값의 차를 기준으로 하는 비교자 - 추이성을 위배합니다.
* 이 방식은 사용하면 안 됩니다.
* 이 방식은 정수 오퍼플로를 일으키거나 IEEE 754 부동소수점 계산 방식에 따른 오류를 낼 수 있습니다.

## 올바른 방식
```
static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) {
        return Integer.compare(o1.hashCode(), o2.hashCode());
    }
};
```
```
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```

## 핵심 정리
> 순서를 고려해야 하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현하여, 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야 합니다. compareTo 메서드에서 필드의 값을 비교할 때 <와 > 연산자는 쓰지 말아야 합니다. 그 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용해야 합니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)