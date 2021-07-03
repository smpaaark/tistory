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
                .attributes(response)
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
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%202%20-%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0%20%EC%84%A4%EC%A0%95%20%EB%93%B1%EB%A1%9D/1.PNG)

## 네이버 로그인 동의 화면
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%202%20-%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0%20%EC%84%A4%EC%A0%95%20%EB%93%B1%EB%A1%9D/2.PNG)

## 로그인 성공 후 화면
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%202%20-%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0%20%EC%84%A4%EC%A0%95%20%EB%93%B1%EB%A1%9D/3.PNG)

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)