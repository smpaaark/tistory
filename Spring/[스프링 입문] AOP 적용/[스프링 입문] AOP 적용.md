* AOP: Aspect Oriented Programming
* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리

![1]()

## 시간 측정 AOP 등록
```
package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws  Throwable {
        long start = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
        }
    }

}
```
(TimeTraceAop.java)

## 해결
* 회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리합니다.
* 시간을 측정하는 로직을 별도의 공통 로직으로 만들었습니다.
* 핵심 관심 사항을 깔끔하게 유지할 수 있습니다.
* 변경이 필요하면 이 로직만 변경하면 됩니다.
* 원하는 적용 대상을 선택할 수 있습니다.

## 스프링의 AOP 동작 방식 설명
### AOP 적용 전 의존관계   
![2]()   

### AOP 적용 후 의존관계
![3]()   

### AOP 적용 전 전체 그림
![4]()   

### AOP 적용 후 전체 그림
![5]()   

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)