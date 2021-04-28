스프링부트 2.2.x 이상 버전을 사용하면 기본으로 JUnit5가 설치됩니다.   
그래서 JUnit4 방식으로 테스트 코드를 작성하면 에러가 발생하게 됩니다.   
이럴 경우 다음과 같은 의존성을 추가해줘야 합니다.

## 의존성 추가
Maven 기준으로 pom.xml에 아래 의존성 코드를 추가합니다.
```
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
    <scope>test</scope>
</dependency>
```

## 결과
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
