자바처럼 가비지 컬렉터를 갖춘 언어를 사용하면 자칫 메모리 관리에 더 이상 신경 쓰지 않아도 된다고 오해할 수 있는데, 절대 사실이 아닙니다.

## 스택
```
package com.smpaaark.item7;

import java.util.*;

// 코드 7-1 메모리 누수가 일어나는 위치는 어디인가? (36쪽)
public class Stack {

    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }

    /**
     * 원소를 위한 공간을 적어도 하나 이상 확보한다.
     * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
     */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

}
```
(Stack.java)
* '메모리 누수'가 발생합니다.
* 이 코드에서는 스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않습니다.
* 여기서 다 쓴 참조란 문자 그대로 앞으로 다시 쓰지 않을 참조를 뜻합니다.

객체 참조 하나를 살려두면 가비지 컬렉터는 그 객체뿐 아니라 그 객체가 참조하는 모든 객체(그리고 또 그 객체들이 참조하는 모든 객체...)를 회수해가지 못합니다.

## 해결 방법
해당 참조를 다 썼을 때 null 처리(참조 해제)하면 됩니다.   
```
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // 다 쓴 참조 해제
    return result;
}
```
* 제대로 구현한 pop 메서드
* null 처리한 참조를 실수로 사용하려 하면 프로그램은 즉시 NullPointerException을 던지며 종료됩니다(미리 null 처리하지 않았다면 아무 내색 없이 무언가 잘못된 일을 수행할 것입니다).

```객체 참조를 null 처리하는 일은 예외적인 경우여야 합니다.``` 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것입니다.

## null 처리를 해야 하는 경우
가비지 컬렉터가 보기에는 비활성 영역에서 참조하는 객체도 똑같이 유효한 객체입니다. 그러므로 프로그래머는 비활성 영역이 되는 순간 null 처리해서 해당 객체를 더는 쓰지 않을 것임을 가비지 컬렉터에 알려야 합니다.

``` 일반적으로 자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 합니다.``` 원소를 다 사용한 즉시 그 원소가 참조한 객체들을 다 null 처리해줘야 합니다.

## 캐시
```캐시 역시 메모리 누수를 일으키는 주범입니다.``` 객체 참조를 캐시에 넣고 나서, 이 사실을 까맣게 잊은 채 그 객체를 다 쓴 뒤로도 한참을 그냥 놔두는 일을 자주 접할 수 있습니다. 운 좋게 캐시 외부에서 키(key)를 참조하는 동안만(값이 아닙니다) 엔트리가 살아 있는 캐시가 필요한 상황이라면 WeakHashMap을 사용해 캐시를 만들면 됩니다. 다 쓴 엔트리는 그 즉시 자동으로 제거될 것입니다. 단, WeakHashMap은 이러한 상황에서만 유용하다는 사실을 기억해야 합니다.

캐시를 만들 때 보통은 캐시 엔트리의 유효 기간을 정확히 정의하기 어렵기 때문에 시간이 지날수록 엔트리의 가치를 떨어뜨리는 방식을 흔히 사용합니다. 이런 방식에서는 쓰지 않는 엔트리를 이따금 청소해줘야 하는데, (ScheduledThreadPoolExecutor 같은) 백그라운드 스레드를 활용하거나 캐시에 새 엔트리를 추가할 때 부수 작업으로 수행하는 방법이 있습니다.

## 리스너(listener) 혹은 콜백(callback)
클라이언트가 콜백을 등록만  하고 명확히 해지하지 않는다면, 뭔가 조치해주지 않는 한 콜백은 계속 쌓여갈 것입니다. 이럴 때 콜백을 약한 참조(weak reference)로 저장하면 가비지 컬렉터가 즉시 수거해갑니다. 예를 들어 WeakHashMap에 키로 저장하면 됩니다.

## 핵심 정리
> 메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있습니다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 합니다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요합니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)