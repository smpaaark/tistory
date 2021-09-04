* JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해줍니다.
* JPA를 사용하면, SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환을 할 수 있습니다.
* JPA를 사용하면 개발 생산성을 크게 높일 수 있습니다.

## build.gradle 파일에 JPA, h2 데이터베이스 관련 라이브러리 추가
```
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	runtimeOnly 'com.h2database:h2'

	compileOnly("org.springframework.boot:spring-boot-devtools")

	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```
* ```spring-boot-starter-data-jpa```는 내부에 jdbc 관련 라이브러리를 포함합니다. 따라서 jdbc는 제거해도 됩니다.

## 스프링 부트에 JPA 설정 추가
```
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=none
```
(application.properties)
> 주의: 스프링 부트 2.4부터는 ```spring.datasource.username=sa```를 꼭 추가해주어야 합니다. 그렇지 않으면 오류가 발생합니다.

* ```show-sql```: JPA가 생성하는 SQL을 출력합니다.
* ```ddl-auto```: JPA는 테이블을 자동으로 생성하는 기능을 제공하는데 ```none```를 사용하면 해당 기능을 끕니다.
  * ```create```를 사용하면 엔티티 정보를 바탕으로 테이블도 직접 생성해줍니다.

## JPA 엔티티 매핑
```
package hello.hellospring.domain;

import javax.persistence.*;

@Entity
public class Member {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username")
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}
```
(Member.java)

## JPA 회원 리포지토리
```
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import javax.persistence.EntityManager;
import java.util.List;
import java.util.Optional;

public class JpaMemberRepository implements MemberRepository {

    private final EntityManager em;

    public JpaMemberRepository(EntityManager em) {
        this.em = em;
    }

    @Override
    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }

    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = em.createQuery("select m from Member m where m.name = :name", Member.class)
                .setParameter("name", name)
                .getResultList();

        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return em.createQuery("select m from Member m", Member.class).getResultList();
    }

}
```
(JpaMemberRepository.java)

## 서비스 계층에 트랜잭션 추가
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

}
```
(MemberService.java)
* ```org.springframework.transaction.annotation.Transactional```를 사용합니다.
* 스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을 커밋합니다. 만약 런타임 예외가 발생하면 롤백합니다.
* **JPA를 통한 모든 데이터 변경은 트랜잭션 안에서 실행해야 합니다.**

## JPA를 사용하도록 스프링 설정 변경
```
package hello.hellospring;

import hello.hellospring.repository.JpaMemberRepository;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.persistence.EntityManager;

@Configuration
public class SpringConfig {

    private EntityManager em;

    @Autowired
    public SpringConfig(EntityManager em) {
        this.em = em;
    }

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
//        return new JdbcMemberRepository(dataSource);
//        return new JdbcTemplateMemberRepository(dataSource);
        return new JpaMemberRepository(em);
    }

}
```
(SpringConfig.java)

## 콘솔 쿼리 확인
```
Hibernate: select member0_.id as id1_0_, member0_.name as name2_0_ from member member0_ where member0_.name=?
Hibernate: insert into member (id, name) values (null, ?)
```

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)