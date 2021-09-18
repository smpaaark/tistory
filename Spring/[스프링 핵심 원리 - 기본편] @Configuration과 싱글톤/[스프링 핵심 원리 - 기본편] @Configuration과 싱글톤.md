## AppConfig의 이상한 점
```
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    ...

}
```
* memberService 빈을 만드는 코드를 보면 ```memberRepository()```를 호출합니다.
  * 이 메서드를 호출하면 ```new MemoryMemberRepository()```를 호출합니다.
* orderService 빈을 만드는 코드도 동일하게 ```memberRepositoy()```를 호출합니다.
  * 이 메서드를 호출하면 ```new MemoryMemberRepository()```를 호출합니다.

결과적으로 각각 다른 2개의 ```MemoryMemberRepository```가 생성되면서 싱글톤이 깨지는 것 처럼 보입니다.

## 테스트 코드
```
class ConfigurationSingletonTest {

    @Test
    public void configurationTest() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

        MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
        OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
        MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);

        MemberRepository memberRepository1 = memberService.getMemberRepository();
        MemberRepository memberRepository2 = orderService.getMemberRepository();

        System.out.println("memberService -> memberRepository = " + memberRepository1);
        System.out.println("orderService -> memberRepository = " + memberRepository2);
        System.out.println("memberRepository = " + memberRepository);

        assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
        assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
    }

}
```
* 확인해보면 MemoryMemberRepository 인스턴스는 모두 같은 인스턴스가 공유되어 사용됩니다.

## AppConfig에 호출 로그 남김
```
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    ...

}
```
* 출력 결과는 모두 1번만 호출됩니다.
```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
```

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)