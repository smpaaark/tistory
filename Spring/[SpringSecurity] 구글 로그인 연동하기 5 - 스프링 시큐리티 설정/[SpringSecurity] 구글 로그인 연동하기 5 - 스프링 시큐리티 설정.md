## 의존성 추가
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
```
spring-boot-starter-oauth2-client
* 소셜 로그인 등 클라이언트 입장에서 소셜 기능 구현 시 필요한 의존성이다.
* spring-security-oauth2-client와 spring-security-oauth2-jose를 기본적으로 관리해준다.

## SecuriyConfig 클래스 생성
```
import com.usedcar.admin.domain.user.Role;
import lombok.RequiredArgsConstructor;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@RequiredArgsConstructor
@EnableWebSecurity //
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final CustomOAuth2UserService customOAuth2UserService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf().disable()
                .headers().frameOptions().disable() //

                .and()
                    .authorizeRequests() //
                    .antMatchers("/", "/css/**", "/images/**",
                            "/js/**", "/h2-console/**").permitAll() //
                    .antMatchers("/api/**").hasRole(Role.USER.name())
                    .anyRequest().authenticated()

                .and()
                    .logout()
                        .logoutSuccessUrl("/")

                .and()
                    .oauth2Login()
                        .userInfoEndpoint()
                            .userService(customOAuth2UserService);
    }
}
```
@EnableWebSecurity
* Spring Security 설정들을 활성화시켜 준다.

.csrf().disable().headers().frameOptions().disable()
* h2-console 화면을 사용하기 위해 해당 옵션들을 disable 한다.

authorizeRequests
* URL별 권한 관리를 설정하는 옵션의 시작점이다.
* authorizeRequests가 선언되어야만 antMatchers 옵션을 사용할 수 있다.

antMatchers
* 권한 관리 대상을 지정하는 옵션이다.
* URL, HTTP 메소드별로 관리가 가능하다.
* "/" 등 지정된 URL들은 permitAll() 옵션을 통해 전체 열람 권한을 주었다.
* "/api/**" 주소를 가진 API는 USER 권한을 가진 사람만 가능하도록 했다.


## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)