Cloneable은 복제해도 되는 클래스임을 명시하는 용도의 믹스인 인터페이스(mixin interface)지만, 아쉽게도 의도한 목적을 제대로 이루지 못했습니다. 가장 큰 문제는 clone 메서드가 선언된 곳이 Cloneable이 아닌 Object이고, 그마저도 protected라는 데 있습니다. 그래서 Cloneable을 구현하는 것만으로는 외부 객체에서 clone 메서드를 호출할 수 없습니다.

## Cloneable 인터페이스가 하는 일
메서드 하나 없는 Cloneable 인터페이스는 놀랍게도 Object의 protected 메서드인 clone의 동작 방식을 결정합니다. Cloneable을 구현한 클래스의 인스턴스에서 clone을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환하며, 그렇지 않은 클래스의 인스턴스에서 호출하면 CloneNotSupportedException을 던집니다. 이는 인터페이스를 상당히 이례적으로 사용한 예이니 따라 하면 안됩니다.

명세에서는 이야기하지 않지만 ```실무에서 Cloneable을 구현한 클래스는 clone 메서드를 public으로 제공하며, 사용자는 당연히 복제가 제대로 이뤄지리라 기대합니다.``` 생성자를 호출하지 않고도 객체를 생성할 수 있게 되는 것입니다. 하지만 깨지기 쉽고, 위험하고, 모순적인 메커니즘입니다.

## clone 메서드의 허술한 일반 규약
> 이 객체의 복사본을 생성해 반환합니다. '복사'의 정확한 뜻은 그 객체를 구현한 클래스에 따라 다를 수 있습니다. 일반적인 의도는 다음과 같습니다. 어떤 객체 x에 대해 다음 식은 참입니다.
>
> x.clone() != x
> 
> 또한 다음 식도 참입니다.
>
> x.clone().getClass() == x.getClass()
>
> 하지만 이상의 요구를 반드시 만족해야 하는 것은 아닙니다.
> 한편 다음 식도 일반적으로 참이지만, 역시 필수는 아닙니다.
>
> x.clone().equals(x)
>
> 관례상, 이 메서드가 반환하는 객체가 super.clone을 호출해 얻어야 합니다. 이 클래스와 (Object를 제외한) 모든 상위 클래스가 이 관례를 따른다면 다음 식은 참입니다.
>
> x.clone().getClass() == x.getClass()
>
> 관례상, 반환된 객체와 원본 객체는 독립적이어야 합니다. 이를 만족하려면 super.clone으로 얻은 객체의 필드 중 하나 이상을 반환 전에 수정해야 할 수도 있습니다.
* 강제성이 없다는 점만 빼면 생성자 연쇄(constructor chaining)와 살짝 비슷한 메커니즘입니다. 즉, clone 메서드가 super.clone이 아닌, 생성자를 호출해 얻은 인스턴스를 반환해도 컴파일러는 불평하지 않을 것입니다. 하지만 이 클래스의 하위 클래스에서 super.clone을 호출한다면 잘못된 클래스의 객체가 만들어져, 결국 하위 클래스의 clone 메서드가 제대로 동작하지 않게 됩니다.

## clone 메서드 구현 - 가변 객체 참조 X
```
@Override public PhoneNumber clone() {
    try {
        return (PhoneNumber) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();  // 일어날 수 없는 일이다.
    }
}
```
* 가변 상태를 참조하지 않는 클래스용 clone 메서드
* 이 메서드가 동작하게 하려면 PhoneNumber의 클래스 선언에 Cloneable을 구현한다고 추가해야 합니다.
* 재정의한 메서드의 반환 타입은 상위 클래스의 메서드가 반환하는 타입의 하위 타입일 수 있습니다.
* 이 방식으로 클라이언트가 형변환하지 않아도 되게끔 해주는 것이 좋습니다.

### CloneNotSupportedException
super.clone 호출을 try-catch 블록으로 감싼 이유는 Object의 clone 메서드가 검사 예외(checked exception)인 CloneNotSupportedException을 던지도록 선언되었기 때문입니다.

## clone 메서드 구현 - 가변 객체 참조 O
클래스가 가변 객체를 참조하는 순간 재앙으로 돌변합니다.
```
public class Stack {

    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    // 원소를 위한 공간을 적어도 하나 이상 확보한다.
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

    // clone이 동작하는 모습을 보려면 명령줄 인수를 몇 개 덧붙여서 호출해야 한다.
    public static void main(String[] args) {
        Stack stack = new Stack();
        for (String arg : args)
            stack.push(arg);
        Stack copy = stack.clone();
        while (!stack.isEmpty())
            System.out.print(stack.pop() + " ");
        System.out.println();
        while (!copy.isEmpty())
            System.out.print(copy.pop() + " ");
    }

}
```
* clone 메서드가 단순히 super.clone의 결과를 그대로 반환하면 반환된 Stack 인스턴스의 size 필드는 올바른 값을 갖겠지만, elements 필드는 원본 Stack 인스턴스와 똑같은 배열을 참조할 것입니다.
* 원본이나 복제중 하나를 수정하면 다른 하나도 수정되어 불변식을 해친다는 이야기입니다.

```clone 메서드는 사실상 생성자와 같은 효과를 냅니다. 즉, clone은 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야합니다.``` 그래서 Stack의 clone 메서드는 제대로 동작하려면 스택 내부 정보를 복사해야 하는데, 가장 쉬운 방법은 elements 배열의 clone을 재귀적으로 호출해주는 것입니다.
```
@Override public Stack clone() {
    try {
        Stack result = (Stack) super.clone();
        result.elements = elements.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```
* 가변 상태를 참조하는 클래스용 Clone 메서드
* 배열의 clone은 런타임 타입과 컴파일타임 타입 모두가 원본 배열과 똑같은 배열을 반환합니다.
* 따라서 배열을 복제할 때는 배열의 clone 메서드를 사용하라고 권장합니다.

elements 필드가 final이었다면 앞서의 방식은 작동하지 않습니다. final 필드에는 새로운 값을 할당할 수 없기 때문입니다. 이는 근본적인 문제로, 직렬화와 마찬가지로 ```Cloneable 아키텍처는 '가변 객체를 참조하는 필드는 final로 선언하라'는 일반 용법과 충돌합니다```(단, 원본과 복제된 객체가 그 가변 객체를 공유해도 안전하다면 괜찮습니다). 

## 해시테이블용 clone 메서드
clone을 재귀적으로 호출하는 것만으로는 충분하지 않을 때도 있습니다. 해시테이블 내부는 버킷들의 배열이고, 각 버킷은 키-값 쌍을 담는 연결 리스트의 첫 번째 엔트리를 참조합니다.
```
public class HashTable implements Cloneable {

    private Entry[] buckets = ...;

    private static class Entry {

        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }

    }

    ... // 나머지 코드는 생략

}
```

```
@Override public HashTable clone() {
    try {
        HashTable result = (HashTable) super.clone();
        result.buckets = buckets.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```
* 잘못된 clone 메서드 - 가변 상태를 공유합니다.
* 복제본은 자신만의 버킷 배열을 갖지만, 이 배열은 원본과 같은 연결 리스트를 참조하여 원본과 복제본 모두 예기치 않게 동작할 가능성이 생깁니다.
* 이를 해결하려면 각 버킷을 구성하는 연결 리스트를 복사해야 합니다.

```
public class HashTable implements Cloneable {

    private Entry[] buckets = ...;

    private static class Entry {

        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }

        // 이 엔트리가 가리키는 연결 리스트를 재귀적으로 복사
        Entry deepCopy() {
            return new Entry(key, value, next == null ? null : next.deepCopy());
        }

    }

    @Override public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for (int i = 0; i < buckets.length; i++) {
                if (buckets[i] != null) {
                    result.buckets[i] = buckets[i].deepCopy();
                }
            }
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }

    ... // 나머지 코드는 생략

}
```
* private 클래스인 HashTable.Entry는 깊은 복사(deep copy)를 지원하도록 보강되었습니다.
* HashTable의 clone 메서드는 먼저 적절한 크기의 새로운 버킷 배열을 할당한 다음 원래의 버킷 배열을 순회하며 비지 않은 각 버킷에 대해 깊은복사를 수행합니다.
* 이 때 Entry의 deepCopy 메서드는 자신이 가리키는 연결 리스트 전체를 복사하기 위해 자신을 재귀적으로 호출합니다.
* 하지만 이 방법은 재귀 호출 때문에 리스트의 원소 수만큼 스택 프레임을 소비하여, 리스트가 길면 스택 오버플로를 일으킬 위험이 있습니다.
* 이 문제를 피하려면 deepCopy를 재귀 호출 대신 반복자를 써서 순회하는 방향으로 수정해야 합니다.

```
Entry deepCopy() {
    Entry result = new Entry(key, value, next);
    for (Entry p = result; p.next != null; p = p.next) {
        p.next = new Entry(p.next.key, p.next.value, p.next.next);
    }
    return result;
}
```
* 엔트리 자신이 가리키는 연결 리스트를 반복적으로 복사합니다.

## 복잡한 가변 객체를 복제하는 또 다른 방법
먼저 super.clone을 호출하여 얻은 객체의 모든 필드를 초기 상태로 설정한 다음, 원본 객체의 상태를 다시 생성하는 고수준 메서드를 호출합니다. 고수준 API를 활용해 복제하면 보통은 간단하고 제법 우아한 코드를 얻게 되지만, 아무래도 저수준에서 바로 처리할 때보다는 느립니다. 또한, Cloneable 아키텍처의 기초가 되는 필드 단위 객체 복사를 우회하기 때문에 전체 Cloneable 아키텍처와는 어울리지 않은 방식이기도 합니다.

## 주의점
생성자에서는 재정의될 수 있는 메서드를 호출하지 않아야 하는데 clone 메서드도 마찬가지입니다. 만약 clone이 하위 클래스에서 재정의한 메서드를 호출하면, 하위 클래스는 복제 과정에서 자신의 상태를 교정할 기회를 잃게 되어 원본과 복제본의 상태가 달라질 가능성이 큽니다.

Object의 clone 메서드는 CloneNotSupportedException을 던진다고 선언했지만 재정의한 메서드는 그렇지 않습니다. ```public인 clone 메서드에서는 throws 절을 없애야 합니다.``` 검사 예외를 던지지 않아야 그 메서드를 사용하기 편하기 때문입니다.

상속용 클래스는 Cloneable을 구현해서는 안 됩니다. Object의 방식을 모방하여 Cloneable 구현 여부를 하위 클래스에서 선택하도록 해주거나, clone을 동작하지 않게 구현해놓고 하위 클래스에서 재정의하지 못하게 할 수도 있습니다.
```
@Override
protected final Object clone() throws CloneNotSupportedException {
    throw new CloneNotSupportedException();
}
```
* 하위 클래스에서 Cloneable을 지원하지 못하게 하는 clone 메서드

Cloneable을 구현한 스레드 안전 클래스를 작성할 때는 clone 메서드 역시 적절히 동기화해줘야 합니다. Object의 clone 메서드는 동기화를 신경 쓰지 않았습니다.

## 요약
Cloneable을 구현하는 모든 클래스는 clone을 재정의해야 합니다. 이때 접근 제한자는 public으로, 반환 타입은 클래스 자신으로 변경합니다. 이 메서드는 가장 먼저 super.clone을 호출한 후 필요한 필드를 전부 적절히 수정합니다. 기본 타입 필드와 불변 객체 참조만 갖는 클래스라면 아무 필드도 수정할 필요가 없습니다. 단, 일련번호나 고유 ID는 비록 기본 타입이나 불변일지라도 수정해줘야 합니다.

## 복사 생성자와 복사 팩터리
```복사 생성자와 복사 팩터리라는 더 나은 객체 복사 방식을 제공할 수 있습니다.``` 복사 생성자란 단순히 자신과 같은 클래스의 인스턴스를 인수로 받는 생성자를 말합니다.
```
public Yum(Yum yum) { ... };
```
* 복사 생성자

```
public static Yum newInstance(Yum yum) { ... };
```
* 복사 팩터리
* 복사 생성자를 모방한 정적 팩터리입니다.

복사 생성자와 그 변형인 복사 팩터리는 Cloneable/clone 방식보다 더 나은 면이 많습니다. 언어 모순적이고 위험천만한 객체 생성 메커니즘(생성자를 쓰지 않는 방식)을 사용하지 않으며, 엉성하게 문서화된 규약에 기대지 않고, 정상적인 final 필드 용법과도 충돌하지 않으며, 불필요한 검사 예외를 던지지 않고, 형변환도 필요치 않습니다.

복사 생성자와 복사 팩터리는 해당 클래스가 구현한 '인터페이스' 타입의 인스턴스를 인수로 받을 수 있습니다. 인터페이스 기반 복사 생성자와 복사 팩터리의 더 정확한 이름은 '변환 생성자(conversion constructor)'와 '변환 팩터리(conversion factory)' 입니다. 이들을 이용하면 클라이언트는 원본의 구현 타입에 얽매이지 않고 복제본의 타입을 직접 선택할 수 있습니다.

## 핵심 정리
> Cloneable이 몰고 온 모든 문제를 되짚어봤을 때, 새로운 인터페이스를 만들 때는 절대 Cloneable을 확장해서는 안 되며, 새로운 클래스도 이를 구현해서는 안 됩니다. final 클래스라면 Cloneable을 구현해도 위험이 크지 않지만, 성능 최적화 관점에서 검토한 후 별다른 문제가 없을 때만 드물게 허용해야 합니다. 기본 원칙은 '복제 기능은 생성자와 팩터리를 이용하는 게 최고'라는 것입니다. 단, 배열만은 clone 메서드 방식이 가장 깔끔한, 이 규칙의 합당한 예외라 할 수 있습니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)