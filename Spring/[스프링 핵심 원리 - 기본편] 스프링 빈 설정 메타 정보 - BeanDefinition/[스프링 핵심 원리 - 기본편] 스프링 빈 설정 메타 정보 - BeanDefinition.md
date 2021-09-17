* 스프링의 다양한 설정 형식을 지원하는데 그 중심에는 ```BeanDefinition```이라는 추상화가 있습니다.
* 쉽게 이야기해서 ```역할과 구현을 개념적으로 나눈 것```입니다.
  * XML을 읽어서 BeanDefinition을 만들면 됩니다.
  * 자바 코드를 읽어서 BeanDefinition을 만들면 됩니다.
  * 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 됩니다. 오직 BeanDefinition만 알면 됩니다.
* ```BeanDefinition```을 빈 설정 메타정보라 합니다.
  * ```@Bean```, ```<bean>```당 각각 하나씩 메타 정보가 생성됩니다.
* 스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성합니다.   

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%ED%95%B5%EC%8B%AC%20%EC%9B%90%EB%A6%AC%20-%20%EA%B8%B0%EB%B3%B8%ED%8E%B8%5D%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%B9%88%20%EC%84%A4%EC%A0%95%20%EB%A9%94%ED%83%80%20%EC%A0%95%EB%B3%B4%20-%20BeanDefinition/1.PNG)

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%ED%95%B5%EC%8B%AC%20%EC%9B%90%EB%A6%AC%20-%20%EA%B8%B0%EB%B3%B8%ED%8E%B8%5D%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%B9%88%20%EC%84%A4%EC%A0%95%20%EB%A9%94%ED%83%80%20%EC%A0%95%EB%B3%B4%20-%20BeanDefinition/2.PNG)   
* ```AnnotationConfigApplicationContext```는 ```AnnotatedBeanDefinitionReader```를 사용해서 ```AppConfig.class```를 읽고 ```BeanDefinition```을 생성합니다.
* ```GenericXmlApplicationContext```는 ```XmlBeanDefinitionReader```를 사용해서 ```appConfig.xml```설정 정보를 읽고 ```BeanDefinition```을 생성합니다.
* 새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 ```BeanDefinition```을 생성하면 됩니다.

## BeanDefinition 살펴보기
### BeanDefinition 정보
* BeanClassName: 생성할 빈의 클래스 명(자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)
* factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름, 예)appConfig
* factoryMethodName: 빈을 생성할 팩토리 메서드 지정, 예) memberService
* Scope: 싱글톤(기본값)
* lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때까지 최대한 생성을 지연처리 하는지 여부
* InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
* DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
* Constructor arguments, Properties: 의존관계 주입에서 사용합니다. (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)

## 정리
* BeanDefinition을 직접 생성해서 스프링 컨테이너에 등록할 수 도 있습니다. 하지만 실무에서 BeanDefinition을 직접 정의하거나 사용할 일은 거의 없습니다.
* BeanDefinition에 대해서는 너무 깊이있게 이해하기 보다는, 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 됩니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)