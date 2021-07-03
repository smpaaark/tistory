## OAuthAttributes 코드 추가
구글 로그인을 등록하면서 대부분 코드가 확장성 있게 작성되었다 보니 네이버는 쉽게 등록 가능합니다.   
다음과 같이 네이버인지 판단하는 코드와 네이버 생성자만 추가해 주면 됩니다.
```
@Getter
public class OAuthAttributes {
    ...

    public static OAuthAttributes of(String registrationId, String userNameAttributeName, Map<String, Object> attributes) {
        if ("naver".equals(registrationId)) {
            return ofNaver("id", attributes);
        }

        return ofGoogle(userNameAttributeName, attributes);
    }

    ...

    private static OAuthAttributes ofNaver(String userNameAttributeName, Map<String, Object> attributes) {
        Map<String, Object> response = (Map<String, Object>) attributes.get("response");

        return OAuthAttributes.builder()
                .name((String) response.get("name"))
                .email((String) response.get("email"))
                .picture((String) response.get("profile_image"))
                .attributes(attributes)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }

    ...

}
```

## 네이버 로그인 버튼 추가
index.mustache
```
{{^name}}
            <p class="lead">🔐Login</p>
            <p>
                <a href="/oauth2/authorization/google" class="btn btn-lg btn-primary">Google Login</a>
                <a href="/oauth2/authorization/naver" class="btn btn-lg btn-success">Naver Login</a>
            </p>
        {{/name}}
```
/oauth2/authorization/naver
* 네이버 로그인 URL은 application-oauth.properties에 등록한 redirect-uri 값에 맞춰 자동으로 등록됩니다.
* /oauth2/authorization/ 까지는 고정이고 마지막 path만 각 소셜 로그인 코드를 사용하면 됩니다.

## 로그인 화면
![1]()

## 네이버 로그인 동의 화면
![2]()

## 로그인 성공 후 화면
![3]()

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)