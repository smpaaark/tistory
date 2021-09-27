상속은 코드를 재사용하는 강력한 수단이지만, 항상 최선은 아닙니다. 상위 클래스와 하위 클래스를 모두 같은 프로그래머가 통제하는 패키지 안에서라면 상속도 안전한 방법입니다. 확장할 목적으로 설계되었고 문서화도 잘 된 클래스도 마찬가지로 안전합니다. 하지만 일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구현 클래스를 상속하는 일은 위험합니다.

## 재정의 문제
```메서드 호출과 달리 상속은 캡슐화를 깨뜨립니다.``` 다르게 말하면, 상위 클래스가 어떻게 구현되었느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있습니다. 따라서 상위 클래스 설계자가 확장을 충분히 고려하고 문서화도 제대로 해두지 않으면 하위 클래스는 상위 클래스의 변화에 발맞춰 수정돼야만 합니다.

### 예시
변형된 HashSet을 만들어 추가된 원소의 수를 저장하는 변수와 접근자 메서드를 추가했습니다.
```
public class InstrumentedHashSet<E> extends HashSet<E> {

    // 추가된 원소의 수
    private int addCount = 0;

    public InstrumentedHashSet() {
    }

    public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }

    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

}
```
* 잘못된 예 - 상속을 잘못 사용했습니다.

```
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("틱", "탁탁", "펑"));
```
* getAddCount 메서드를 호출하면 3을 반환하리라 기대하겠지만, 실제로는 6을 반환합니다.
* 원인은 HashSet의 addAll 메서드가 add 메서드를 사용해 구현된 데 있습니다.
* 따라서 addCount에 값이 중복해서 더해져, 최종값이 6으로 늘어난 것입니다.

이처럼 자신의 다른 부분을 사용하는 '자기사용(self-use)' 여부는 해당 클래스의 내부 구현 방식에 해당하며, 자바 플랫폼 전반적인 정책인지, 그래서 다음 릴리스에서도 유지될지는 알 수 없습니다.

addAll 메서드를 다른 식으로 재정의할 수도 있겠지만, 상위 클래스의 메서드 동작을 다시 구현하는 이 방식은 어렵고, 시간도 더 들고, 자칫 오류를 내거나 성능을 떨어뜨릴 수 있습니다. 또한, 하위 클래스에서 접근할 수 없는 private 필드를 써야 하는 상황이라면 이 방식으로는 구현 자체가 불가능합니다.

다음 릴리스에서 상위 클래스에 추가되는 모든 원소가 특정 조건을 만족해야만 하는 검사 메서드를 추가한다면 그 컬렉션을 상속하여 추가된 모든 메서드를 재정의 하면 될 것 같습니다. 하지만 이 방식이 통하는 것은 상위 클래스에 또 다른 원소 추가 메서드가 만들어지기 전 까지입니다.

## 재정의 대신 새로운 메서드 추가
이 방식이 훨씬 안전한 것은 맞지만, 위험이 전혀 없는 것은 아닙니다. 다음 릴리스에서 상위 클래스에 새 메서드가 추가됐는데, 운 없게도 하필 여러분이 하위 클래스에 추가한 메서드와 시그니처가 같고 반환 타입은 다르다면 여러분의 클래스는 컴파일조차 되지 않습니다. 혹, 반환 타입 마저 같다면 상위 클래스의 새 메서드를 재정의한 꼴이니 앞서의 문제와 똑같은 상황에 부닥칩니다. 

## 컴포지션(compositon; 구성)
기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 합니다. 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션이라 합니다. 새 클래스의 메서드들은 (private 필드를 참조로하는) 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환합니다. 이 방식을 전달(forwarding)이라 하며, 새 클래스의 메서드들을 전달 메서드(forwarding method)라 부릅니다. 그 결과 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않습니다. 

### 예시
```
public class InstrumentedSet<E> extends ForwardingSet<E> {

    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    public int getAddCount() {
        return addCount;
    }

}
```
* 래퍼 클래스 - 상속 대신 컴포지션을 사용했습니다.

```
public class ForwardingSet<E> implements Set<E> {

    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s; }

    public void clear()               { s.clear();            }
    public boolean contains(Object o) { return s.contains(o); }
    public boolean isEmpty()          { return s.isEmpty();   }
    public int size()                 { return s.size();      }
    public Iterator<E> iterator()     { return s.iterator();  }
    public boolean add(E e)           { return s.add(e);      }
    public boolean remove(Object o)   { return s.remove(o);   }
    public boolean containsAll(Collection<?> c)
    { return s.containsAll(c); }
    public boolean addAll(Collection<? extends E> c)
    { return s.addAll(c);      }
    public boolean removeAll(Collection<?> c)
    { return s.removeAll(c);   }
    public boolean retainAll(Collection<?> c)
    { return s.retainAll(c);   }
    public Object[] toArray()          { return s.toArray();  }
    public <T> T[] toArray(T[] a)      { return s.toArray(a); }
    @Override public boolean equals(Object o)
    { return s.equals(o);  }
    @Override public int hashCode()    { return s.hashCode(); }
    @Override public String toString() { return s.toString(); }

}
```
* 재사용할 수 있는 전달 클래스

상속 방식은 구체 클래스 각각을 따로 확장해야 하며, 지원하고 싶은 상위 클래스의 생성자 각각에 대응하는 생성자를 별도로 정의해줘야 합니다. 하지만 지금 선보인 컴포지션 방식은 한 번만 구현해두면 어떠한 Set 구현체라고 계측할 수 있으며, 기존 생성자들과도 함께 사용할 수 있습니다.
```
Set<Instant> times = new InstrumentedSet<>(new TreeSet<>(cmp));
Set<E> s = new InstrumentedSet<>(new HashSet<>(INIT_CAPACITY));
```



## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)