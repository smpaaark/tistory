### src/main/resources/ 디렉토리에 application-oauth.properties 파일을 생성한다.
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%202%20-%20application-oauth%20%EB%93%B1%EB%A1%9D/1.PNG)

### 해당 파일에 클라이언트 ID(clientId)와 클라이언트 보안 비밀(clientSecret) 코드를 다음과 같이 등록한다.
```
spring.security.oauth2.client.registration.google.client-id=발급 받은 클라이언트 ID
spring.security.oauth2.client.registration.google.client-secret=발급 받은 클라이언트 보안 비밀
spring.security.oauth2.client.registration.google.scope=profile,email
```
scope=profile,email
* scope의 기본 값은 openid, profile, email이다.
* 강제로 ```profile,email```를 등록한 이유는 openid라는 scoper가 있으면 Open Id Provider로 인식하기 때문이다.
* 이렇게 되면 OpenId Provider인 서비스(구글)와 그렇지 않은 서비스(네이버/카카오 등)로 나눠서 각각 OAuth2Service를 만들어야 한다.
* 하나의 OAuth2Service로 사용하기 위해 일부러 openid scope를 빼고 등록한다.

### application.properties에서 application-oauth.properties를 포함하도록 구성한다.
스프링 부트에서는 properties의 이름을 application-XXX.properties로 만들면 xxx라는 이름의 profile이 생성되어 이를 통해 관리할 수 있다.   
즉, profile=xxx라는 식으로 호출하면 ```해당 properties의 설정들을 가져올``` 수 있다.   
호출하는 방식은 여러 방식이 있지만 여기서는 스프링 부트의 기본 설정 파일인 application.properties에서 application-oauth.properties를 포함하도록 구성한다.   

application.properties에 다음과 같이 코드를 추가한다.
```
spring.profiles.include=oauth
```

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)