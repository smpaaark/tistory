```equals를 재정의한 클래스 모두에서 hashCode도 재정의해야 합니다.``` 그렇지 않으면 hashCode 일반 규약을 어기게 되어 해당 클래스의 인스턴스를 HashMap이나 HashSet 같은 컬렉션의 원소로 사용할 때 문제를 일으킬 것입니다.

## Object 명세에서 발췌한 규약
> * equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 사용되는 동안 그 객체의 hashCode 메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 합니다. 단, 애플리케이션을 다시 실행한다면 이 값이 달라져도 상관 없습니다.
> * equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 합니다.
> * equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없습니다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아집니다.

```hashCode 재정의를 잘못했을 때 크게 문제가 되는 조항은 두 번째입니다. 즉, 논리적으로 같은 객체는 같은 해시코드를 반환해야 합니다.```
```
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "제니");
```
* 이 코드 다음에 m.get(new PhoneNumber(707, 867, 5309))를 실행하면 "제니"가 나와야 할 것 같지만, 실제로는 null을 반환합니다.
* PhoneNumber 클래스는 hashCode를 재정의하지 않았기 때문에 논리적 동치인 두 객체가 서로 다른 해시코드를 반환하여 두 번재 규약을 지키지 못합니다.
* HashMap은 해시코드가 다른 엔트리끼리는 동치성 비교를 시도조차 하지 않도록 최적화되어 있습니다.

## hashCode 메서드 작성
### 안 좋은 해시 함수 예시
```
@Override public int hashCode() {return 42;}
```
* 최악의 (하지만 적법한) hashCode 구현 - 사용 금지
* 모든 객체에서 똑같은 값만 내어주므로 모든 객체가 해시테이블의 버킷 하나에 담겨 마치 연결 리스트(linked list)처럼 동작합니다.
* 그 결과 평균 수행 시간이 O(1)인 해시테이블이 O(n)으로 느려져서, 객체가 많아지면 도저히 쓸 수 없게 됩니다.

### 좋은 해시 함수
좋은 해시 함수라면 서로 다른 인스턴스에 다른 해시코드를 반환합니다. 이것이 바로 hashCode의 세 번째 규약이 요구하는 속성입니다. 이상적인 해시 함수는 주어진 (서로 다른) 인스턴스들을 32비트 정수 범위에 균일하게 분배해야 합니다.

### 좋은 hashCode 작성 요령
1. int 변수 result를 선언한 후 값 c로 초기화합니다. 이때 c는 해당 객체의 첫번째 핵심 필드를 단계 2.a 방식으로 계산한 해시코드입니다(여기서 핵심 필드란 equals 비교에 사용되는 필드를 말합니다.).
2. 해당 객체의 나머지 핵심 필드 f 각각에 대해 다음 작업을 수행합니다.
  a. 해당 필드의 해시코드 c를 계산합니다.
    　i. 기본 타입 필드라면, Type.hashCode(f)를 수행합니다. 여기서 Type은 해당 기본 타입의 박싱 클래스입니다.
    　ii. 참조 타입 필드면서 이 클래스의 equals 메서드가 이 필드의 equals를 재귀적으로 호출해 비교한다면, 이 필드의 hashCode를 재귀적으로 호출합니다. 계산이 더 복잡해질 것 같으면, 이 필드의 표준형(canonical representation)을 만들어 그 표준형의 hashCode를 호출합니다. 필드의 값이 null이면 0을 사용합니다(다른 상수도 괜찮지만 전통적으로 0을 사용합니다).
    　iii. 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룹니다. 이상의 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드를 계산한 다음, 단계 2.b 방식으로 갱신합니다. 배열에 핵심 원소가 하나도 없다면 단순히 상수(0을 추천합니다)를 사용합니다. 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용합니다.
  b. 단계 2.a에서 계산한 해시코드 c로 result를 갱신합니다. 코드로는 다음과 같습니다.
     result = 31 * result + c;
3. result를 반환합니다.

hashCode를 구현했다면 이 메서드가 동치인 인스턴스에 대해 똑같은 해시코드를 반환하는지 검증할 단위 테스트를 작성합니다(equals와 hashCode 메서드를 AutoValue로 생성했다면 건너뛰어도 좋습니다).

다른 필드로부터 계산해낼 수 있는 파생 필드는 해시코드 계산에서 제외해도 됩니다. 또한 equals 비교에 사용되지 않은 필드는 '반드시' 제외해야 합니다. 그렇지 않으면 hashCode 규약 두 번째를 어기게될 위험이 있습니다.

단계 2.b의 곱셈 31 * result는 필드를 곱하는 순서에 따라 result 값이 달라지게 합니다. 그 결과 클래스에 비슷한 필드가 여러 개일 때 해시 효과를 크게 높여줍니다. 곱할 숫자를 31로 정한 이유는 31이 홀수이면서 소수(prime)이기 때문입니다. 만약 이 숫자가 짝수이고 오버플로가 발생한다면 정보를 잃게 됩니다.

### 좋은 해시 함수 예시
```
@Override public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
```
* 전형적인 hashCode 메서드
* 비결정적(undeterministic) 요소는 전혀 없으므로 동치인 PhoneNumber 인스턴스들은 같은 해시코드를 가질 것이 확실합니다.

## 구아바
해시 충돌이 더욱 적은 방법을 꼭 써야 한다면 구아바의 com.google.common.hash.Hashing을 참고합니다.

## Object.hash
Object 클래스는 임의의 개수만큼 객체를 받아 해시코드를 계산해주는 정적 메서드인 hash를 제공합니다. 하지만 아쉽게도 속도는 더 느립니다. 입력 인수를 담기 위한 배열이 만들어지고, 입력 중 기본 타입이 있다면 박싱과 언박싱도 거쳐야 하기 때문입니다.
```
@Override public int hashCode() {
    return Objects.hash(lineNum, prefix, areaCode);
}
```
* 한 줄짜리 hashCode 메서드 - 성능이 살짝 아쉽습니다.

## 캐싱 방식
클래스가 불변이고 해시코드를 계산하는 비용이 크다면, 매번 새로 계산하기 보다는 캐싱하는 방식을 고려해야 합니다. 이 타입의 객체가 주로 해시의 키로 사용될 것 같다면 인스턴스가 만들어질 때 해시코드를 계산해둬야 합니다.

## 지연 초기화(lazy initialization) 전략
해시에 사용되지 않는 경우라면 hashCode가 처음 불릴 때 계산하는 지연 초기화 전략을 사용할 수 있습니다. 필드를 지연 초기화하려면 그 클래스를 스레드 안전하게 만들도록 신경 써야 합니다.
```
private int hashCode; // 자동으로 0으로 초기화된다.

@Override public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNum);
        hashCode = result;
    }
    return result;
}
```
* 해시코드를 지연 초기화하는 hashCode 메서드 - 스레드 안정성까지 고려해야 합니다.
* hashCode 필드의 초깃값은 흔히 생성되는 객체의 해시코드와는 달라야 합니다.

## 주의점
```성능을 높인답시고 해시코드를 계산할 때 핵심 필드를 생략해서는 안 됩니다.``` 속도야 빨라지겠지만, 해시 품질이 나빠져 해시테이블의 성능을 심각하게 떨어뜨릴 수도 있습니다.

```hashCode가 반환하는 값의 생성 규칙을 API 사용자에게 자세히 공표하지 말아야 합니다. 그래야 클라이언트가 이 값에 의지하지 않게 되고, 추후에 계산 방식을 바꿀 수 있습니다.```

## 핵심 정리
> equals를 재정의할 때는 hashCode도 반드시 재정의해야 합니다. 그렇지 않으면 프로그램이 제대로 동작하지 않을 것입니다. 재정의한 hashCode는 Object의 API 문서에 기술된 일반 규약을 따라야 하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야 합니다. 이렇게 구현하기가 어렵지는 않지만 조금 따분한 일이긴 합니다. 하지만 걱정하지 않아도 됩니다. AutoValue 프레임워크를 사용하면 멋진 equals와 hashCode를 자동으로 만들어줍니다. IDE들도 이런 기능을 일부 제공합니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)