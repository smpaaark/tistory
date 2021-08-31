정적 팩터리와 생성자는 선택적 매개변수가 많을때 적절히 대응하기 어렵습니다.

## 점층적 생성자 패턴(telescoping constructor pattern)
필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자, 선택 매개변수를 2개까지 받는 생성자, ... 형태로 선택 매개변수를 전부 다 받는 생성자까지 늘려가는 방식입니다.
```
package com.smpaaark.item2;

public class NutritionFacts {

    private final int servingSize;  // (mL, 1회 제공량)     필수
    private final int servings;     // (회, 총 n회 제공량)  필수
    private final int calories;     // (1회 제공량당)       선택
    private final int fat;          // (g/1회 제공량)       선택
    private final int sodium;       // (mg/1회 제공량)      선택
    private final int carbohydrate; // (g/1회 제공량)       선택

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }
    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize  = servingSize;
        this.servings     = servings;
        this.calories     = calories;
        this.fat          = fat;
        this.sodium       = sodium;
        this.carbohydrate = carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola =
                new NutritionFacts(240, 8, 100, 0, 35, 27);
    }

}
```
(NutritionFacts.java)
* 점층적 생성자 패턴 - 확장하기 어렵습니다.
* 이 클래스의 인스턴스를 만들려면 원하는 매개변수를 모두 포함한 생성자 중 가장 짧은 것을 골라 호출하면 됩니다.
```
NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
```
* 보통 이런 생성자는 사용자가 설정하길 원치 않는 매개변수까지 포함하기 쉬운데, 어쩔 수 없이 그런 매개변수에도 값을 지정해줘야 합니다.

### 요약
```점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵습니다.```
* 코드를 읽을 때 각 값의 의미가 무엇인지 헷갈릴 것이고, 매개변수가 몇 개인지도 주의해서 세어 보아야 할 것입니다.

## 자바빈즈 패턴(JavaBeans pattern)
매개변수가 없는 생성자로 객체를 만든 후, 세터(setter) 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식입니다.
```
package com.smpaaark.item2.javabeans;

public class NutritionFacts {

    // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.
    private int servingSize  = -1; // 필수; 기본값 없음
    private int servings     = -1; // 필수; 기본값 없음
    private int calories     = 0;
    private int fat          = 0;
    private int sodium       = 0;
    private int carbohydrate = 0;

    public NutritionFacts() { }

    // Setters
    public void setServingSize(int val)  { servingSize = val; }

    public void setServings(int val)     { servings = val; }

    public void setCalories(int val)     { calories = val; }

    public void setFat(int val)          { fat = val; }

    public void setSodium(int val)       { sodium = val; }

    public void setCarbohydrate(int val) { carbohydrate = val; }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts();
        cocaCola.setServingSize(240);
        cocaCola.setServings(8);
        cocaCola.setCalories(100);
        cocaCola.setSodium(35);
        cocaCola.setCarbohydrate(27);
    }

}
```
(NutritionFacts.java)
* 자바빈즈 패턴 - 일관성이 깨지고, 불변으로 만들 수 없습니다.
* 점층적 생성자 패턴의 단점들이 자바빈즈 패턴에서는 더이상 보이지 않습니다.
```
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
```
* 하지만 ```자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 됩니다.```
* 이처럼 일관성이 무너지는 문제 때문에 ```자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없으며 스레드 안전성을 얻으려면 프로그래머가 추가 작업을 해줘야만 합니다.```
* 이러한 단점을 완화하고자 생성이 끝난 객체를 수동으로 '얼리고(freezing)' 얼리기 전에는 사용할 수 없도록 하기도 합니다.

## 빌더 패턴(Builder pattern)
점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성을 겸비한 패턴입니다. 빌더는 생성할 클래스 안에 정적 멤버 클래스로 만들어두는 게 보통입니다.
1. 클라이언트는 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자(혹은 정적 팩터리)를 호출해 빌더 객체를 얻습니다.
2. 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정합니다.
3. 매개변수가 없는 build 메서드를 호출해 드디어 우리에게 필요한 (보통은 불변인) 객체를 얻습니다.
```
package com.smpaaark.item2.builder;

public class NutritionFacts {

    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {

        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }

        public Builder fat(int val)
        { fat = val;           return this; }

        public Builder sodium(int val)
        { sodium = val;        return this; }

        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }

}
```
(NutritionFacts.java)
* 빌더 패턴 - 점층적 생성자 패턴과 자바빈즈 패턴의 장점만 취합니다.
* 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있습니다.
```
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
```
* 이 클라이언트 코드는 쓰기 쉽고, 무엇보다도 읽기 쉽습니다.
* ```빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것입니다.```

잘못된 매개변수를 최대한 일찍 발견하려면 빌더의 생성자와 메서드에서 입력 매개변수를 검사하고, build 메서드가 호출하는 생성자에서 여러 매개변수에 걸친 불변식(invariant)을 검사해야 합니다.

### 불변(immutable 혹은 immutability)
> 어떠한 변경도 허용하지 않는다는 뜻으로, 주로 변경을 허용하는 가변(mutable) 객체와 구분하는 용도로 쓰입니다. 대표적으로 String 객체는 한번 만들어지면 절대 값을 바꿀 수 없는 불변 객체입니다.

### 불변식(invariant)
> 프로그램이 실행되는 동안, 혹은 정해진 기간 동안 반드시 만족해야 하는 조건을 말합니다. 다시 말해 변경을 허용할 수는 있으나 주어진 조건 내에서만 허용한다는 뜻입니다. 예컨대 리스트의 크기는 반드시 0이상이어야 하니, 만약 한순간이라도 음수 값이 된다면 불변식이 깨진 것입니다. 또한, 기간을 표현하는 Period 클래스에서 start 필드의 값은 반드시 end 필드의 값보다 앞서야 하므로, 두 값이 역전되면 역시 불변식이 깨진 것입니다. 따라서 가변 객체에도 불변식은 존재할 수 있으며, 넓게 보면 불변은 불변식의 극단적인 예라 할 수 있습니다.

```빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰이기 좋습니다.```   
각 계층의 클래스에 빌더를 멤버로 정의합니다. 추상 클래스는 추상 빌더를, 구체 클래스(concrete class)는 구체 빌더를 갖게 합니다.
```
package com.smpaaark.item2.hierarchical;

// 코드 2-4 계층적으로 설계된 클래스와 잘 어울리는 빌더 패턴 (19쪽)

// 참고: 여기서 사용한 '시뮬레이트한 셀프 타입(simulated self-type)' 관용구는
// 빌더뿐 아니라 임의의 유동적인 계층구조를 허용한다.

import java.util.EnumSet;
import java.util.Objects;
import java.util.Set;

public abstract class Pizza {

    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        // 하위 클래스는 이 메서드를 재정의(overriding)하여
        // "this"를 반환하도록 해야 한다.
        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone(); // 아이템 50 참조
    }

}
```
(Pizza.java)
* 계층적으로 설계된 클래스와 잘 어울리는 빌더 패턴
* Pizza.Builder 클래스는 재귀적 타입 한정을 이용하는 제네릭 타입입니다.
* 여기에 추상 메서드인 self를 더해 하위 클래스에서 형변환하지 않고도 메서드 연쇄를 지원할 수 있습니다.
  * self 타입이 없는 자바를 위한 이 우회 방법을 시뮬레이트한 셀프 타입(simulated self-type) 관용구라 합니다.

```
package com.smpaaark.item2.hierarchical;

import java.util.Objects;

// 코드 2-5 뉴욕 피자 - 계층적 빌더를 활용한 하위 클래스 (20쪽)
public class NyPizza extends Pizza {

    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        @Override protected Builder self() { return this; }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }

    @Override public String toString() {
        return toppings + "로 토핑한 뉴욕 피자";
    }

}
```
(NyPizza.java)

```
package com.smpaaark.item2.hierarchical;

// 코드 2-6 칼초네 피자 - 계층적 빌더를 활용한 하위 클래스 (20~21쪽)
public class Calzone extends Pizza {

    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false; // 기본값

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override public Calzone build() {
            return new Calzone(this);
        }

        @Override protected Builder self() { return this; }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }

    @Override public String toString() {
        return String.format("%s로 토핑한 칼초네 피자 (소스는 %s에)",
                toppings, sauceInside ? "안" : "바깥");
    }

}
```
(Calzone.java)

* 각 하위 클래스의 빌더가 정의한 build 메서드는 해당하는 구체 하위 클래스를 반환하도록 합니다.

```
NyPizza pizza = new NyPizza.Builder(SMALL)
        .addTopping(SAUSAGE).addTopping(ONION).build();
Calzone calzone = new Calzone.Builder()
        .addTopping(HAM).sauceInside().build();

System.out.println(pizza);
System.out.println(calzone);
```
* 생성자로 누릴 수 없는 사소한 이점으로, 빌더를 이용하면 가변인수(varargs) 매개변수를 여러 개 사용할 수 있습니다.

빌더 패턴은 상당히 유연합니다. 빌더 하나로 여러 객체를 순회하면서 만들 수있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있습니다. 객체마다 부여되는 일련번호와 같은 특정 필드는 빌더가 알아서 채우도록 할 수도 있습니다.

## 빌더 패턴의 단점
* 객체를 만들려면, 그에 앞서 빌더부터 만들어야 합니다.
* 점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 합니다.

## 핵심 정리
> ```생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫습니다.```   
매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇습니다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전합니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)