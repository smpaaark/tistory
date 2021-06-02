스프링부트 2.2.x 이상 버전을 사용하면 기본으로 JUnit5가 설치됩니다.   
그래서 JUnit4 방식으로 테스트 코드를 작성하면 에러가 발생하게 됩니다.   
이럴 경우 다음과 같은 의존성을 추가해줘야 합니다.

## 의존성 추가
Maven 기준으로 pom.xml에 아래 의존성 코드를 추가합니다.
```
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <scope>test</scope>
</dependency>
```

## 결과
JUnit4 테스트 코드
```
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {

    @Autowired
    MemberRepository memberRepository;

    @Test
    @Transactional
    @Rollback(false)
    public void testMember() throws Exception {
        // given
        Member member = new Member();
        member.setUsername("memberA");

        // when
        Long saveId = memberRepository.save(member);
        Member findMember = memberRepository.find(saveId);

        // then
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
        Assertions.assertThat(findMember).isEqualTo(member);
        System.out.println("findMember == member: " + (findMember == member));
    }
}
```

테스트 정상 작동    
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Maven%20%26%20Gradle/%5BMaven%5D%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%B6%80%ED%8A%B8%202.2.x%20%EC%9D%B4%EC%83%81%20%EB%B2%84%EC%A0%84%EC%97%90%EC%84%9C%20JUnit4%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/1.PNG)
