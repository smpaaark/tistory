## 싱글턴
인스턴스를 오직 하나만 생성할 수 있는 클래스를 말합니다. 그런데 ```클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있습니다.``` 타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면 싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없기 때문입니다.

## 싱글턴을 만드는 방식
보통 두 가지 방식을 사용하는데 두 방식 모두 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둡니다.
1. public static 멤버가 final 필드인 방식
```
package com.smpaaark.item3.field;

// 코드 3-1 public static final 필드 방식의 싱글턴 (23쪽)
public class Elvis {

    public static final Elvis INSTANCE = new Elvis();

    private Elvis() { }

    public void leaveTheBuilding() {
        System.out.println("Whoa baby, I'm outta here!");
    }

    // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
    public static void main(String[] args) {
        Elvis elvis = Elvis.INSTANCE;
        elvis.leaveTheBuilding();
    }

}
```
(Elvis.java)
* public static final 필드 방식의 싱글턴
* private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한번만 호출됩니다.
* 예외는 단 한가지, 권한이 있는 클라이언트는 리플렉션 API인 AccessibleObject.setAccessible을 사용해 private 생성자를 호출할 수 있습니다.

2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식
```
package com.smpaaark.item3.staticfactory;

// 코드 3-2 정적 팩터리 방식의 싱글턴 (24쪽)
public class Elvis {

    private static final Elvis INSTANCE = new Elvis();

    private Elvis() { }

    public static Elvis getInstance() { return INSTANCE; }

    public void leaveTheBuilding() {
        System.out.println("Whoa baby, I'm outta here!");
    }

    // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
    public static void main(String[] args) {
        Elvis elvis = Elvis.getInstance();
        elvis.leaveTheBuilding();
    }

}
```
(Elvis.java)
* 정적 팩터리 방식의 싱글턴
* Elvis.getInstance는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스란 결코 만들어지지 않습니다. (역시 리플렉션을 통한 예외는 똑같이 적용됩니다.)

### public static 멤버가 final 필드인 방식의 장점
* 해당 클래스가 싱글턴임이 API에 명백히 드러납니다.
* 간결합니다.

### 정적 팩터리 메서드를 public static 멤버로 제공하는 방식의 장점
* (마음이 바뀌면) API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있습니다.
  * 유일한 인스턴스를 반환하던 팩터리 메서드가 (예컨대) 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있습니다.
* 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있습니다.
* 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있습니다.

### 싱글턴 클래스를 직렬화하는 방법
단순히 Serializable을 구현한다고 선언하는 것만으로는 부족합니다. 모든 인스턴스 필드를 일시적(transient)이라고 선언하고 readResolve 메서드를 제공해야 합니다.
```
// 싱글턴임을 보장해주는 readReslove 메서드
private Object readResolve() {
    // '진짜' Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡깁니다.
    return INSTANCE;
}
```

3. 원소가 하나인 열거 타입을 선언하는 방식
```
package com.smpaaark.item3.enumtype;

// 열거 타입 방식의 싱글턴 - 바람직한 방법 (25쪽)
public enum Elvis {

    INSTANCE;

    public void leaveTheBuilding() {
        System.out.println("기다려 자기야, 지금 나갈께!");
    }

    // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
    public static void main(String[] args) {
        Elvis elvis = Elvis.INSTANCE;
        elvis.leaveTheBuilding();
    }

}
```
(Elvis.java)
* 열거 타입 방식의 싱글턴 - 바람직한 방법
* public 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화할 수 있고, 심지어 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아줍니다.
* 조금 부자연스러워 보일 수는 있으나 ```대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법입니다.```
* 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없습니다(열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있습니다).

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)