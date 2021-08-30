클래스는 생성자와 별도로 정적 팩터리 메서드(static factory method)를 제공할 수 있습니다. 그 클래스의 인스턴스를 반환하는 단순한 정적 메서드를 말합니다.
* 예시
```
public static Boolean valueOf(boolean b) {
    return (b ? TRUE : FALSE);
}
```
(Boolean 클래스)
* 이 메서드는 기본 타입인 boolean 값을 받아 Boolean 객체 참조로 반환해줍니다.

> 정적 팩터리 메서드는 디자인패턴에서의 팩터리 메서드(Factory Method)와 다릅니다. 디자인 패턴 중에 일치하는 패턴은 없습니다.

## 정적 팩터리 메서드의 장점
1. ```이름을 가질 수 있습니다.```
  * 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있습니다.
  * 한 클래스에 시그니처가 같은 생성자가 여러 개 필요할 것 같으면, 생성자를 정적 팩터리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주어야 합니다.
2. ```호출될 때마다 인스턴스를 새로 생성하지 않아도 됩니다.```
  * 불변 클래스(immutble class)는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있습니다.
  * 반복되는 요청에 같은 객체를 반환하는 식으로 정적 팩터리 방식의 클래스는 언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있습니다.
3. ```반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있습니다.```
  * 이 능력은 반환할 객체의 클래스를 자유롭게 선택할 수 있게 하는 '엄청난 유연성'을 선물합니다.
  * API를 만들 때 이 유연성을 응용하면 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있습니다.
  * 자바 8부터는 인터페이스가 정적 메서드를 가질 수 없다는 제한이 풀렸기 때문에 인스턴스화 불가 동반 클래스를 둘 이유가 별로 없습니다.
4. ```입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있습니다.```
  * 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없습니다.
5. ```정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됩니다.```
  * 이러한 유연함은 서비스 제공자 프레임워크(service provider framework)를 만드는 근간이 됩니다.
  * 서비스 제공자 프레임워크는 3개의 핵심 컴포넌트로 이뤄집니다.
    * 서비스 인터페이스(service interface): 구현체의 동작을 정의
    * 제공자 등록 API(provider registration API): 제공자가 구현체를 등록할 때 사용
    * 서비스 접근 API(service access API): 클라이언트가 서비스의 인스턴스를 얻을 때 사용
  * 3개의 핵심 컴포넌트와 더불어 종종 서비스 제공자 인터페이스(service provider interface)라는 네 번째 컴포넌트가 쓰이기도 합니다.
    * 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명해줍니다.

## 정적 팩터리 메서드의 단점
1. ```상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없습니다.```
  * 이 제약은 상속보다 컴포지션을 사용하도록 유도하고 불변 타입으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있습니다.
2. ```정적 팩터리 메서드는 프로그래머가 찾기 어렵습니다.```
  * 생성자처럼 API 설명에 명확히 드러나지 않으니 사용자는 정적 팩터리 메서드 방식 클래스를 인스턴스화할 방법을 알아내야 합니다.
  * 따라서 API 문서를 잘 써놓고 메서드 이름도 널리 알려진 규약을 따라 짓는 식으로 문제를 완화해야 합니다.

## 정적 팩토리 메서드 명명 방식(관례)
* from: 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드
  * 예) Date d = Date.from(instant);
* of: 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
  * 예) Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
* valueOf: from과 of의 더 자세한 버전
  * 예) BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
* instance 혹은 getInstance: (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않습니다.
  * 예) StackWalker luke = StackWalker.getInstance(options);
* create 혹은 newInstance: instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장합니다.
  * 예) Object newArray = Array.newInstance(classObject, arrayLen);
* getType: getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 씁니다. "Type"은 팩터리 메서드가 반환할 객체의 타입입니다.
  * 예) FileStore fs = Files.getFileStore(path);
* newType: newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 씁니다. "Type"은 팩터리 메서드가 반환할 객체의 타입입니다.
  * 예) BufferedReader br = Files.newBufferedReader(path);
* type: getType과 newType의 간결한 버전
  * 예) List<Complaint> litany = Collections.list(legacyLitany);

## 핵심 정리
```
정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋습니다. 그렇다고 하더라도 정적 팩터리를 사용하는 게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고쳐야 합니다.
```

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)