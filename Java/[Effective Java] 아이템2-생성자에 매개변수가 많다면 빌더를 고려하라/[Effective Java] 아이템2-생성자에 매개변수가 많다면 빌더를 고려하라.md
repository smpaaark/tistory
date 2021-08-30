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
* 점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성을 겸비한 패턴입니다.
* 빌더는 생성할 클래스 안에 정적 멤버 클래스로 만들어두는 게 보통입니다.
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

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)