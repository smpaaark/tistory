먼저 [네이버 오픈 API](https://developers.naver.com/apps/#/register?api-nvlogin)로 이동합니다.   

다음과 같이 각 항목을 채웁니다.    
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%201%20-%20%EB%84%A4%EC%9D%B4%EB%B2%84%20API%20%EB%93%B1%EB%A1%9D/1.PNG)  

URL을 등록합니다.     
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%201%20-%20%EB%84%A4%EC%9D%B4%EB%B2%84%20API%20%EB%93%B1%EB%A1%9D/2.PNG)

ClientID와 ClientSecret이 생성됩니다.   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%201%20-%20%EB%84%A4%EC%9D%B4%EB%B2%84%20API%20%EB%93%B1%EB%A1%9D/3.PNG) 

해당 키값들을 application-oauth.properties에 등록합니다.   
네이버에서는 스프링 시큐리티를 공식 지원하지 않기 때문에 그동안 Common-OAuth2Provier에서 해주던 값들도 전부 수동으로 입력해야 합니다.
```
# registration
spring.security.oauth2.client.registration.naver.client-id=네이버클라이언트ID
spring.security.oauth2.client.registration.naver.client-secret=네이버클라이언트시크릿
spring.security.oauth2.client.registration.naver.redirect-uri={baseUrl}/{action}/oauth2/code/{registrationId}
spring.security.oauth2.client.registration.naver.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.naver.scope=name,email,profile_image
spring.security.oauth2.client.registration.naver.client-name=Naver

# provider
spring.security.oauth2.client.provider.naver.authorization-uri=https://nid.naver.com/oauth2.0/authorize
spring.security.oauth2.client.provider.naver.token-uri=https://nid.naver.com/oauth2.0/token
spring.security.oauth2.client.provider.naver.user-info-uri=https://openapi.naver.com/v1/nid/me
spring.security.oauth2.client.provider.naver.user-name-attribute=response //
```
user-name-attribute=response
* 기준이 되는 user-name의 이름을 네이버에서는 response로 해야 합니다.
* 이유는 네이버의 회원 조회 시 반환되는 JSON 형태 때문입니다.
* 스프링 시큐리티에서는 하위 필드를 명시할 수 없습니다.
* 최상위 필드들만 user-name으로 지정 가능합니다.
* 하지만 네이버의 응답값 최상위 필드는 resultCode, message, response 입니다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)
* [[SpringSecurity] 구글 로그인 연동하기 1 - 구글 서비스 등록](https://smpark1020.tistory.com/203)