* 스프링 컨테이너는 다양한 형식의 설정 정보를 받아드릴 수 있게 유연하게 설계되어 있습니다.
  * 자바 코드, XML, Groovy 등등  

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%ED%95%B5%EC%8B%AC%20%EC%9B%90%EB%A6%AC%20-%20%EA%B8%B0%EB%B3%B8%ED%8E%B8%5D%20%EB%8B%A4%EC%96%91%ED%95%9C%20%EC%84%A4%EC%A0%95%20%ED%98%95%EC%8B%9D%20%EC%A7%80%EC%9B%90%20-%20%EC%9E%90%EB%B0%94%20%EC%BD%94%EB%93%9C%2C%20XML/1.PNG)

## 애노테이션 기반 자바 코드 설정 사용
* ```new AnnotationConfigApplicationContext(AppConfig.class)```
* ```AnnotationConfigApplicationContext``` 클래스를 사용하면서 자바 코드로된 설정 정보를 넘기면 됩니다.

## XML 설정 사용
* 최근에는 스프링 부트를 사용하면서 XML기반의 설정은 잘 사용하지 않습니다. 아직 많은 레거시 프로젝트들이 XML로 되어 있고, 또 XML을 사용하면 컴파일 없이 빈 설정 정보를 변경할 수 있는 장점도 있으므로 한번쯤 배워두는 것도 괜찮습니다.
* ```GenericXmlApplicationContext```를 사용하면서 ```xml```설정 파일을 넘기면 됩니다.

## xml 기반의 스프링 빈 설정 정보
```src/main/resources/appConfig.xml```
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="memberService" class="hello.core.member.MemberServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository"/>
    </bean>

    <bean id="memberRepository" class="hello.core.member.MemoryMemberRepository"/>

    <bean id="orderService" class="hello.core.order.OrderServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository"/>
        <constructor-arg name="discountPolicy" ref="discountPolicy"/>
    </bean>

    <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy"/>

</beans>
```
* xml 기반의 스프링 설정 정보와 자바 코드로 된 설정 정보는 거의 비슷하다는 것을 알 수 있습니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)