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
                    .anyRequest().authenticated() //

                .and()
                    .logout()
                        .logoutSuccessUrl("/") //

                .and()
                    .oauth2Login() //
                        .userInfoEndpoint() //
                            .userService(customOAuth2UserService); //
    }
}
```
\@EnableWebSecurity
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

anyRequest
* 설정된 값들 이외 나머지 URL들을 나타낸다.
* 여기서는 authenticated()을 추가하여 나머지 URL들은 모두 인증된 사용자에게만 허용하게 한다.
* 인증된 사용자 즉, 로그인한 사용자들을 이야기한다.

logout().logoutSuccessUrl("/")
* 로그아웃 기능에 대한 여러 설정의 진입점이다.
* 로그아웃 성공 시 / 주소로 이동한다.

oauth2Login
* OAuth2 로그인 기능에 대한 여러 설정의 진입점이다.

userInfoEndpoint
* OAuth2 로그인 성공 이후 사용자 정보를 가져올 때의 설정들을 담당한다.

userService
* 소셜 로그인 성공 후속 조치를 진행할 UserService 인터페이스의 구현체를 등록한다.
* 리소스 서버(즉, 소셜 서비스들)에서 사용자 정보를 가져온 상태에서 추가로 진행하고자 하는 기능을 명시할 수 있다.

## CustomOAuth2UserService 클래스 생성
구글 로그인 이후 가져온 사용자의 정보(email, name, picture 등)들을 기반으로 가입 및 정보수정, 세션 저장 등의 기능을 지원한다.   
구글 사용자 정보가 업데이트 되었을 때를 대비하여 update 기능도 같이 구현되었다.   
사용자의 이름(name)이나 프로필 사진(picture)이 변경되면 User 엔티티에도 반영된다.   
```
@RequiredArgsConstructor
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {

    private final UserRepository userRepository;
    private final HttpSession httpSession;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
        OAuth2UserService<OAuth2UserRequest, OAuth2User> delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);

        String registrationId = userRequest.getClientRegistration().getRegistrationId(); //
        String userNameAttributeName = userRequest
                .getClientRegistration().getProviderDetails()
                .getUserInfoEndpoint().getUserNameAttributeName(); //

        OAuthAttributes attributes = OAuthAttributes.of(registrationId, userNameAttributeName, oAuth2User.getAttributes()); //

        User user = saveOrUpdate(attributes);

        httpSession.setAttribute("user", new SessionUser(user)); //

        return new DefaultOAuth2User(Collections.singleton(new SimpleGrantedAuthority(user.getRoleKey())),
                attributes.getAttributes(),
                attributes.getNameAttributeKey());
    }

    private User saveOrUpdate(OAuthAttributes attributes) {
        User user = userRepository.findByEmail(attributes.getEmail())
                .map(entity -> entity.update(attributes.getName(), attributes.getPicture()))
                .orElse(attributes.toEntity);

        return userRepository.save(user);
    }
    
}
```
registrationId
* 현재 로그인 진행 중인 서비스를 구분하는 코드이다.
* 지금은 구글만 사용하는 불필요한 값이지만, 이후 네이버 로그인 연동 시에 네이버 로그인인지, 구글 로그인인지 구분하기 위해 사용한다.

userNameAttributeName
* OAuth2 로그인 진행 시 키가 되는 필드값을 이야기한다.
  * Primary Key와 같은 의미이다.
* 구글의 경우 기본적으로 코드를 지원하지만, 네이버 카카오 등은 기본 지원하지 않는다.
  * 구글의 기본 코드는 "sub"이다.
* 이후 네이버 로그인과 구글 로그인을 동시 지원할 때 사용된다.

OAuthAttributes
* OAuth2UserService를 통해 가져온 OAuth2User의 attribute를 담은 클래스이다.
* 이후 네이버 등 다른 소셜 로그인도 이 클래스를 사용한다.

SessionUser
* 세션에 사용자 정보를 저장하기 위한 Dto 클래스이다.

## OAuthAttributes 클래스 생성
```
@Getter
public class OAuthAttributes {

    private Map<String, Object> attributes;
    private String nameAttributeKey;
    private String name;
    private String email;
    private String picture;

    @Builder
    public OAuthAttributes(Map<String, Object> attributes, String nameAttributeKey, String name, String email, String picture) {
        this.attributes = attributes;
        this.nameAttributeKey = nameAttributeKey;
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

    //
    public static OAuthAttributes of(String registrationId, String userNameAttributeName, Map<String, Object> attributes) {
        return OAuthAttributes.builder()
                .name((String) attributes.get("name"))
                .email((String) attributes.get("email"))
                .picture((String) attributes.get("picture"))
                .attributes(attributes)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }

    //
    public User toEntity() {
        return User.builder()
                .name(name)
                .email(email)
                .picture(picture)
                .role(Role.GUEST)
                .build();
    }

}
```
of
* OAuth2User에서 반환하는 사용자 정보는 Map이기 때문에 값 하나하나를 변환해야만 한다.

toEntity()
* User 엔티티를 생성한다.
* OAuthAttributes에서 엔티티를 생성하는 시점은 처음 가입할 때이다.
* 가입할 때의 기본 권한을 GUEST로 주기 위해서 role 빌더값에는 Role.GUEST를 사용한다.

## SessionUser 클래스 생성
```
@Getter
public class SessionUser implements Serializable {

    private String name;
    private String email;
    private String picture;

    public SessionUser(String name, String email, String picture) {
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

}

```
SessionUser에는 **인증된 사용자 정보**만 필요하다.   
그 외에 필요한 정보들은 없으니 name, email, picture만 필드로 선언한다.   

### User 클래스를 사용하면 안되는 이유
만약 User 클래스를 그대로 사용했다면 다음과 같은 에러가 발생한다.   
```
Failed to convert from type [java.lang.Object] to type [byte[]] for value 'com.usedcar.admin.domain.user.User@4a43d6'
```
이는 세션에 저장하기 위해 User 클래스를 세션에 저장하려고 하니, User 클래스에 **직렬화를 구현하지 않았다**는 의미의 에러이다.   
하지만 User 클래스에 직렬화 코드를 넣는 것은 좋지 않다.   
User 클래스는 엔티티이기 때문에 언제 다른 엔티티와 관계가 형성될지 모른다.   
예를 들어 \@OneToMany, \@ManyToMany 등 자식 엔티티를 갖고 있다면 직렬화 대상에 자식들까지 포함되니 **성능 이슈, 부수 효과**가 발생할 확률이 높다.   
그래서 **직렬화 기능을 가진 세션 Dto**를 하나 추가로 만드는 것이 이후 운영 및 유지보수 때 많은 도움이 된다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)