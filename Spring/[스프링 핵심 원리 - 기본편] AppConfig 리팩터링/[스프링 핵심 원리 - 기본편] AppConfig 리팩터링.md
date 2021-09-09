현재 AppConfig를 보면 ```중복```이 있고, ```역할```에 따른 ```구현```이 잘 안보입니다.

## 기대하는 그림
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%ED%95%B5%EC%8B%AC%20%EC%9B%90%EB%A6%AC%20-%20%EA%B8%B0%EB%B3%B8%ED%8E%B8%5D%20AppConfig%20%EB%A6%AC%ED%8C%A9%ED%84%B0%EB%A7%81/1.PNG)   

## 리팩터링 전
```
public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
    }

}
```
* 중복을 제거하고, 역할에 따른 구현이 보이도록 리팩터링이 필요합니다.

## 리팩터링 후
```
public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    private MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }

}
```
* ```new MemoryMemberRepository()``` 이 부분이 중복 제거되었습니다. 이제 ```MemoryMemberRepository```를 다른 구현체로 변경할 때 한 부분만 변경하면 됩니다.
* ```AppConfig```를 보면 역할과 구현 클래스가 한눈에 들어옵니다. 애플리케이션 전체  구성이 어떻게 되어있는지 빠르게 파악할 수 있습니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)