자바는 두 가지 객체 소멸자를 제공합니다. ```그중 finalizer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요합니다.``` finalizer는 나름의 쓰임새가 몇 가지 있긴 하지만 기본적으로 '쓰지 말아야' 합니다. ```cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요합니다.```

## 자바의 객체 회수
자바에서는 접근할 수 없게 된 객체를 회수하는 역할을 가비지 컬렉터가 담당하고, 프로그래머에게는 아무런 작업도 요구하지 않습니다. 자바에서는 비메모리 자원을 회수하는 용도로 try-with-resources와 try-finally를 사용해 해결합니다.

## finalizer와 cleaner의 문제점
### 수행 시점
finalizer와 cleaner는 즉시 수행된다는 보장이 없습니다. ```즉, finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없습니다.``` 

finalizer나 cleaner를 얼마나 신속히 수행할지는 전적으로 가비지 컬렉터 알고리즘에 달렸으며, 이는 가비지 컬렉터 구현마다 천차만별입니다.

클래스에 finalizer를 달아두면 그 인스턴스의 자원 회수가 제멋대로 지연될 수 있습니다. finalizer 스레드는 다른 애플리케이션 스레드보다 우선 순위가 낮아서 실행될 기회를 제대로 얻지 못하는 경우가 많습니다. 이 문제를 예방할 해법은 딱 하나, finalizer를 사용하지 않는 방법 뿐입니다. 한편, cleaner는 자신을 수행할 스레드를 제어할 수 있다는 면에서 조금 낫습니다. 하지만 여전히 백그라운드에서 수행되며 가비지 컬렉터의 통제하에 있으니 즉각 수행되리라는 보장은 없습니다.

자바 언어 명세는 finalizer나 cleaner의 수행 시점뿐 아니라 수행 여부조차 보장하지 않습니다. 따라서 프로그램 생애주기와 상관없는, ```상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안됩니다.``` 예를 들어 데이터베이스 같은 공유 자원의 영구 락(lock) 해제를 finalizer나 cleaner에 맡겨 놓으면 분산 시스템 자체가 서서히 멈출 것입니다.

System.gc나 System.runFinalization 메서드는 finalizer와 cleaner가 실행될 가능성을 높여줄 수는 있으나, 보장해주진 않습니다. 이를 보장해주겠다는 메서드인 System.runFinalizersOnExit와 그 쌍둥이인 Runtime.runFinalizersOnExit가 있습니다. 하지만 이 두 메서드는 심각한 결함 때문에 수십 년간 지탄받아 왔습니다.

finalizer 동작 중 발생한 예외는 무시되며, 처리할 작업이 남았더라도 그 순간 종료됩니다. 보통의 경우엔 잡지 못한 예외가 스레드를 중단시키고 스택 추적 내역을 출력하겠지만, 같은 일이 finalizer에서 일어난다면 경고조차 출력하지 않습니다. 그나마 cleaner를 사용하는 라이브러리는 자신의 스레드를 통제하기 때문에 이러한 문제가 발생하지 않습니다.

### 성능
```finalizer와 cleaner는 심각한 성능 문제도 동반합니다.``` finalizer가 가비지 컬렉터의 효율을 떨어뜨리기 때문입니다. cleaner도 클래스의 모든 인스턴스를 수거하는 형태로 사용하면 성능은 finalizer와 비슷합니다. 하지만 안정망 형태로만 사용하면 훨씬 빨라집니다. 대신 안정망을 설치하는 대가로 성능이 약 5배 정도 느려집니다.

### 보안
```finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수도 있습니다.``` 생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 됩니다. ```객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 그렇지도 않습니다.``` ```final이 아닌 클래스를 finalizer 공격으로부터 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언해야 합니다.```

## AutoCloseable
파일이나 스레드 등 종료해야 할 자원을 담고 있는 객체의 클래스에서 finalizer나 cleaner를 대신해줄 묘안은 바로 ```AutoCloseable을 구현```해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 됩니다(일반적으로 예외가 발생해도 제대로 종료되도록 try-with-resources를 사용해야 합니다.). 이때 close 메서드에서 이 객체는 더 이상 유효하지 않음을 필드에 기록하고, 다른 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 IllegalStateException을 던져야 합니다.

## cleaner와 finalizer의 적절한 쓰임새
1. 자원의 소유자가 close 메서드를 호출하지 않는 것에 대비한 안전망 역할입니다.
  * cleaner나 finalizer가 즉시 (혹은 끝까지) 호출되리라는 보장은 없지만, 클라이언트가 하지 않은 자원 회수를 늦게라도 해주는 것이 아예 안하는 것보다는 낫습니다.
  * 안전망 역할의 finalizer를 제공하는 클래스: ```FileInputStream, FileOutputStream, ThreadPoolExecutor```
2. 네이티브 피어(native peer)와 연결된 객체에서 사용합니다.
  * 네이티브 피어란 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체를 말합니다.
  * 네이티브 피어는 자바 객체가 아니니 가비지 컬렉터는 그 존재를 알지 못합니다.
  * 단, 성능 저하를 감당할 수 없거나 네이티브 피어가 사용하는 자원을 즉시 회수해야 한다면 앞서 설명한 close 메서드를 사용해야 합니다.

## cleaner 사용 방법
Room 클래스는 AutoCloseable을 구현합니다. finalizer와 달리 cleaner는 클래스의 public API에 나타나지 않습니다.
```
package com.smpaaark.item8;

import java.lang.ref.Cleaner;

public class Room implements AutoCloseable {

    private static final Cleaner cleaner = Cleaner.create();

    private static class State implements Runnable {
        int numJunkPiles;

        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }

        @Override
        public void run() {
            System.out.println("방 청소");
            numJunkPiles = 0;
        }
    }

    private final State state;

    private final Cleaner.Cleanable cleanable;

    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }

    @Override
    public void close() throws Exception {
        cleanable.clean();
    }

}
```
(Room.java)
* cleaner를 안전망으로 활용하는 AutoCloseable 클래스
* static으로 선언된 중첩 클래스인 State는 cleaner가 방을 청소할 때 수거할 자원들을 담고 있습니다.
* State는 Runnable을 구현하고, 그 안의 run 메서드는 cleanable에 의해 딱 한 번만 호출될 것입니다.
* 이 cleanble 객체는 Room 생성자에서 cleaner에 Room과 State를 등록할 때 얻습니다.

### run 메서드가 호출되는 상황
1. 보통은 Room의 close 메서드를 호출할 때 입니다.
2. 혹은 가비지 컬렉터가 Room을 회수할 때까지 클라이언트가 close를 호출하지 않는다면, cleaner가 (바라건대) state의 run 메서드를 호출해줄 것입니다.

### 주의점
State 인스턴스는 '절대로' Room 인스턴스를 참조해서는 안 됩니다. Room 인스턴스를 참조할 경우 순환참조가 생겨 가비지 컬렉터가 Room 인스턴스를 회수해갈 (따라서 자동 청소될) 기회가 오지 않습니다.

## try-with-resources
클라이언트가 모든 Room 생성을 try-with-resources 블록으로 감쌌다면 자동 청소는 전혀 필요하지 않습니다.
```
package com.smpaaark.item8;

// cleaner 안전망을 갖춘 자원을 제대로 활용하는 클라이언트 (45쪽)
public class Adult {

    public static void main(String[] args) {
        try (Room myRoom = new Room(7)) {
            System.out.println("안녕~");
        }
    }

}
```
(Adult.java)
* 기대한 대로 Adult 프로그램은 "안녕~"을 출력한 후, 이어서 "방 청소"를 출력합니다.

### 잘못된 코드
```
package com.smpaaark.item8;

import java.util.concurrent.TimeUnit;

// cleaner 안전망을 갖춘 자원을 제대로 활용하지 못하는 클라이언트 (45쪽)
public class Teenager {

    public static void main(String[] args) {
        new Room(99);
        System.out.println("아무렴");
    }

}
```
(Teenager.java)
* "방 청소"는 한 번도 출력되지 않았습니다.
* 앞서 '예측할 수 없다'고 한 상황입니다.

### cleaner의 명세
> System.exit을 호출할 때의 cleaner 동작은 구현하기 나름입니다. 청소가 이뤄질지는 보장하지 않습니다.

## 핵심 정리
> cleaner(자바 8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용해야합니다. 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 합니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)