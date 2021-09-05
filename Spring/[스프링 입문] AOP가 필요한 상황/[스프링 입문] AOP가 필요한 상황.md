* 모든 메소드의 호출 시간을 측정
* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)
* 회원 가입 시간, 회원 조회 시간 측정

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20AOP%EA%B0%80%20%ED%95%84%EC%9A%94%ED%95%9C%20%EC%83%81%ED%99%A9/1.PNG)   

## MemberService 회원 조회 시간 측정 추가
```
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

@Transactional
public class MemberService {

    ...

    /**
     * 회원 가입
     */
    public Long join(Member member) {

        long start = System.currentTimeMillis();

        try {
            validateDuplicateMember(member); // 중복 회원 검증
            memberRepository.save(member);
            return member.getId();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join = " + timeMs + "ms");
        }
    }

    ...

    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers() {
        long start = System.currentTimeMillis();
        try {
            return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("findMembers = " + timeMs + "ms");
        }
    }

    ...

}
```
(MemberService.java)

## 문제
* 회원 가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아닙니다.
* 시간을 측정하는 로직은 공통 관심 사항입니다.
* 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵습니다.
* 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵습니다.
* 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 합니다.

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)