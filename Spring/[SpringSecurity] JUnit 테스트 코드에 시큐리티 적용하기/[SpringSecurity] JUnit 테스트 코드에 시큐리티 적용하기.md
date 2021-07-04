프로젝트에 스프링 시큐리티를 적용하면 기존에 잘 작동되던 테스트 코드가 권한 부여를 받지 않았기 때문에 실패하게 됩니다.   
따라서 테스트 코드마다 인증한 사용자가 호출한 것처럼 작동하도록 수정이 필요합니다.   

## 소셜 로그인 관련 설정값 추가
테스트 코드를 수행할 때는 src/test/resources/application.properties의 설정값들이 적용됩니다.   
따라서 소셜 로그인 관련 설정값을 추가해줘야 합니다.   

src/test/resources/application.properties
```
# 쿼리 로그 세팅
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.SQL=debug
logging.level.org.hibernate.type=trace
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL57Dialect
spring.jpa.properties.hibernate.dialect.storage_engine=innodb

# DB 세팅
spring.datasource.hikari.jdbc-url=jdbc:h2:mem:testdb;MODE=MYSQL
spring.datasource.hikari.username=sa

# ddl-auto 세팅
spring.jpa.hibernate.ddl-auto=create

# UTF-8 세팅
server.servlet.encoding.charset=UTF-8
server.servlet.encoding.force=true

# 세션 저장소 jdbc 설정
spring.session.store-type=jdbc

# Test OAuth
spring.security.oauth2.client.registration.google.client-id=test
spring.security.oauth2.client.registration.google.client-secret=test
spring.security.oauth2.client.registration.google.scope=profile,email
```

## 임의로 인증된 사용자 추가
현재 테스트 코드들은 사용자 인증이 되지 않은 상태이기 때문에 302 Status Code로 실패합니다.   
따라서 임의로 인증된 사용자를 추가해야 합니다.   

### pom.xml에 의존성 추가
```
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-test</artifactId>
    <scope>test</scope>
</dependency>
```

### 테스트 코드에 임의의 사용자 인증 추가
```
@Test
@WithMockUser(roles = "USER") //
public void 차량_매입하기() throws Exception {
    ...
}
```
@WithMockUser(roles = "USER")
* 인증된 모의(가짜) 사용자를 만들어서 사용합니다.
* roles에 권한을 추가할 수 있습니다.
* 즉, 이 어노테이션으로 인해 ROLE_USER 권한을 가진 사용자가 API를 요청하는 것과 동일한 효과를 가지게 됩니다.

위 처럼 테스트 코드마다 적용해도 되고, 클래스에 한번만 적용해도 해당 클래스의 모든 테스트 코드에 잘 작동한다.
```
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
@WithMockUser(roles = "USER") //
public class CarApiControllerTest {
    ...
}
```

## Maven Test 실행 결과
![1]()

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)