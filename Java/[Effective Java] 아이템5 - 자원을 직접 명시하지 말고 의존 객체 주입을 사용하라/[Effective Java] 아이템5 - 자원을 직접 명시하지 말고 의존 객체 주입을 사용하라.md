많은 클래스가 하나 이상의 자원에 의존합니다. 예시로 맞춤법 검사기는 사전(dictionary)에 의존하는데, 이런 클래스를 정적 유틸리티 클래스로 구현한 모습을 드물지 않게 볼 수 있습니다.
```
public class SpellChecker {

    private static final Lexicon dictionary = ...;

    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ...}

}
```
(SpellChecker.java)
* 정적 유틸리티를 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵습니다.

```
public class SpellChecker {

    private static final Lexicon dictionary = ...;

    private SpellChecker() {}
    public static SpellChecker INSTANCE = new SpellChecker(...);

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ...}

}
```
(SpellChecker.java)
* 싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵습니다.

두 방식 모두 사전을 단 하나만 사용한다고 가정한다는 점에서 그리 훌륭해 보이지 않습니다.

## SpellChecker가 여러 사전을 사용할 수 있도록 만들기
* 간단히 dictionary 필드에서 final 한정자를 제거하고 다른 사전으로 교체하는 메서드를 추가하는 방식
  * 아쉽게도 이 방식은 어색하고 오류를 내기 쉬우며 멀티스레드 환경에서는 쓸 수 없습니다. 
  * ```사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않습니다.```
* ```인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식```
  * 클래스(SpellChecker)가 여러 자원 인스턴스를 지원하며, 클라이언트가 원하는 자원(dictionary)을 사용할 수 있습니다.
  * 이는 의존 객체 주입의 한 형태로, 맞춤법 검사기를 생성할 때 의존 객체인 사전을 주입해주면 됩니다.
```
public class SpellChecker {

    private static final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Object.requireNonNull(dictionary);
    }

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ...}

}
```
(SpellChecker.java)
* 의존 객체 주입 패턴은 유연성과 테스트 용이성을 높여줍니다.
* 불변을 보장하여 (같은 자원을 사용하려는) 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있습니다.

## 변형된 의존 객체 주입 패턴
생성자에 자원 팩터리를 넘겨주는 방식입니다. 팩터리란 호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체를 말합니다. 즉, 팩터리 메서드 패턴(Factory Method pattern)을 구현한 것입니다.
```
Mosaic create(Supplier<? extends Tile> titleFactory) { ... }
```
(자바 8의 Supplier<T> 인터페이스를 입력으로 받는 메서드)

의존 객체 주입이 유연성과 테스트 용이성을 개선해주기는 하지만, 의존성이 수천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 만들기도 합니다. 대거(Dagger), 주스(Guice), 스프링(Spring) 같은 의존 객체 주입 프레임워크를 사용하면 이런 어질러짐을 해소할 수 있습니다.

## 핵심 정리
> 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋습니다. 이 자원들을 클래스가 직접 만들게 해서도 안됩니다. 대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주어야 합니다. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해줍니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)