싱글톤 빈과 프로토타입 빈을 함께 사용할 때, 사용할 때 마다 항상 새로운 프로토타입 빈을 생성하는 방법

## 스프링 컨테이너에 요청
가장 간단한 방법은 싱글톤 빈이 프로토타입을 사용할 때 마다 스프링 컨테이너에 새로 요청하는 것입니다.
```
public class PrototypeProviderTest {

    @Test
    void providerTest() {
        AnnotationConfigApplicationContext ac = new
        AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);
        ClientBean clientBean1 = ac.getBean(ClientBean.class);
        int count1 = clientBean1.logic();
        assertThat(count1).isEqualTo(1);
        ClientBean clientBean2 = ac.getBean(ClientBean.class);
        int count2 = clientBean2.logic();
        assertThat(count2).isEqualTo(1);
    }

    static class ClientBean {

        @Autowired
        private ApplicationContext ac;

        public int logic() {
            PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }

    }

    @Scope("prototype")
    static class PrototypeBean {

        private int count = 0;

        public void addCount() {
            count++;
        }

        public int getCount() {
            return count;
        }

        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init " + this);
        }

        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }

    }

}
```

### 핵심 코드
```
@Autowired
private ApplicationContext ac;

public int logic() {
    PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
* 실행해보면 ```ac.getBean()```을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있습니다.
* 의존관계를 외부에서 주입(DI) 받는게 아니라 이렇게 직접 필요한 의존관계를 찾는 것을 Dependency Lookup (DL) 의존관계 조회(탐색) 이라합니다.
* 그런데 이렇게 스프링의 애플리케이션 컨텍스트 전체를 주입받게 되면, 스프링 컨테이너에 종속적인 코드가 되고, 단위 테스트도 어려워집니다.

## ObjectFactory, ObjectProvider
지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 바로 ```ObjectProvider```입니다. 참고로 과거에는 ```ObjectFactory```가 있었는데, 여기에 편의 기능을 추가해서 ```ObjectProvider```가 만들어졌습니다.
```
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
* 실행해보면 ```prototypeBeanProvider.getObject()```을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있습니다.
* ```ObjectProvider```의 ```getObject()```를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환합니다. (```DL```)
* 스프링이 제공하는 기능을 사용하지만, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워집니다.
* ```ObjectProvider```는 지금 딱 필요한 DL 정도의 기능만 제공합니다.

### 특징
* ObjectFactory: 기능이 단순, 별도의 라이브러리 필요 없음, 스프링에 의존
* ObjectProvider: ObjectFactory 상속, 옵션, 스트림 처리 등 편의 기능이 많고, 별도의 라이브러리 필요 없음, 스프링에 의존

## JSR-330 Provider
마지막 방법은 ```javax.inject.Provider```라는 JSR-330 자바 표준을 사용하는 방법입니다.   
이 방법을 사용하려면 ```javax.inject:javax.inject:1```라이브러리를 gradle에 추가해야 합니다.

### javax.inject.Provider 참고용 코드
```
public interface Provider<T> {

    T get();
    
}
```
```
@Autowired
private Provider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
* 실행해보면 ```provider.get()```을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있습니다.
* ```provider```의 ```get()```을 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환합니다. (```DL```)
* 자바 표준이고, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워집니다.
* ```Provider```는 지금 딱 필요한 DL 정도의 기능만 제공합니다.

### 특징
* ```get()``` 메서드 하나로 기능이 매우 단순합니다.
* 별도의 라이브러리가 필요합니다.
* 자바 표준이므로 스프링이 아닌 다른 컨테이너에서도 사용할 수 있습니다.

## 정리
* 프로토타입 빈은 매번 사용할 때마다 의존관계 주입이 완료된 새로운 객체가 필요할 때 사용하면 됩니다. 그런데 실무에서 웹 애플리케이션을 개발해보면, 싱글톤 빈으로 대부분의 문제를 해결할 수 있기 때문에 프로토타입 빈을 직접적으로 사용하는 일은 매우 드뭅니다.
* ```ObjectProvider```, ```JSR330 Provider``` 등은 프로토타입 뿐만 아니라 DL이 필요한 경우에는 언제든지 사용할 수 있습니다.

> 참고: 스프링이 제공하는 메서드에 ```@Lookup``` 애노테이션을 사용하는 방법도 있지만, 이전 방법들로 충분하고, 고려해야할 내용도 많습니다.

> 참고: ObjectProvider는 DL을 위한 편의 기능을 많이 제공해주고 스프링 외에 별도의 의존관계 추가가 필요 없기 때문에 편리합니다. 만약(정말 그럴일은 거의 없겠지만) 코드를 스프링이 아닌 다른 컨테이너에서도 사용할 수 있어야 한다면 JSR-330 Provider를 사용해야 합니다.

> 스프링을 사용하다 보면 이 기능 뿐만 아니라 다른 기능들도 자바 표준과 스프링이 제공하는 기능이 겹칠때가 많이 있습니다. 대부분 스프링이 더 다양하고 편리한 기능을 제공해주기 때문에, 특별히 다른 컨테이너를 사용할 일이 없다면, 스프링이 제공하는 기능을 사용하면 됩니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)