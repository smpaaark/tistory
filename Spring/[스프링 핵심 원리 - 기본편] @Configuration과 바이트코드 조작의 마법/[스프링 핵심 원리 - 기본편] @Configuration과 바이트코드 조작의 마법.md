스프링 컨테이너는 싱글톤 레지스트리입니다. 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 합니다. 그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용합니다.   
모든 비밀은 ```@Configuration```을 적용한 ```AppConfig```에 있습니다.

```
@Test
void configurationDeep() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    AppConfig bean = ac.getBean(AppConfig.class);

    System.out.println("bean = " + bean.getClass());
}
```
* 사실 ```AnnotationConfigApplicationContext```에 파라미터로 넘긴 값은 스프링 빈으로 등록됩니다. 그래서 ```AppConfig```도 스프링 빈이 됩니다.
* ```AppConfig``` 스프링 빈을 조회해서 클래스 정보를 출력해봅니다.
```
bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$81d53c04
```

순수한 클래스라면 다음과 같이 출력되어야 합니다.
```
class hello.core.AppConfig
```

그런데 예상과는 다르게 클래스 명에 xxxCGLIB가 붙으면서 상당히 복잡해진 것을 볼 수 있습니다. 이것은 내가 만든 클래스가 아니라 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것입니다.

## 그림
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%ED%95%B5%EC%8B%AC%20%EC%9B%90%EB%A6%AC%20-%20%EA%B8%B0%EB%B3%B8%ED%8E%B8%5D%20%40Configuration%EA%B3%BC%20%EB%B0%94%EC%9D%B4%ED%8A%B8%EC%BD%94%EB%93%9C%20%EC%A1%B0%EC%9E%91%EC%9D%98%20%EB%A7%88%EB%B2%95/1.PNG)   

이 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해줍니다. 아마도 다음과 같이 바이트 코드를 조작해서 작성되어 있을 것입니다.(실제로는 CGLIB의 내부 기술을 사용하는데 매우 복잡합니다.)

## AppConfig@CGLIB 예상 코드
```
@Bean
public MemberRepository memberRepository() {
 
    if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
        return 스프링 컨테이너에서 찾아서 반환;
    } else { //스프링 컨테이너에 없으면
        기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
        return 반환
    }
}
```
* \@Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어집니다.
* 덕분에 싱글톤이 보장되는 것입니다.

> 참고: AppConfig@CGLIB는 AppConfig의 자식 타입이므로, AppConfig 타입으로 조회 할 수 있습니다.

## ```@Configuration```을 적용하지 않고, ```@Bean```만 적용할 경우
```
bean = class hello.core.AppConfig
```
이 출력 결과를 통해서 AppConfig가 CGLIB 기술 없이 순수한 AppConfig로 스프링 빈에 등록된 것을 확인할 수 있습니다.

```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
```
이 출력 결과를 통해서 MemberRepository가 총 3번 호출된 것을 알 수 있습니다. 1번은 \@Bean에 의해 스프링 컨테이너에 등록하기 위해서이고, 2번은 각각 ```memberRepository()```를 호출하면서 발생한 코드입니다.

### 인스턴스가 같은지 테스트 결과
```
memberService -> memberRepository = hello.core.member.MemoryMemberRepository@332796d3
orderService -> memberRepository = hello.core.member.MemoryMemberRepository@4f0100a7
memberRepository = hello.core.member.MemoryMemberRepository@3cdf2c61
```
당연히 인스턴스가 같은지 테스트 하는 코드가 실패하고, 각각 다 다른 MemoryMemberRepository 인스턴스를 가지고 있습니다.

## 정리
* \@Bean만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지 않습니다.
  * ```memberRepository()```처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않습니다.
* 크게 고민할 것 없습니다. 스프링 설정 정보는 항상 ```@Configuration```을 사용합니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)