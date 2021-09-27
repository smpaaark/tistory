* Authorization: 클라이언트 인증 정보를 서버에 전달
* WWW-Authenticate: 리소스 접근시 필요한 인증 방법 정의

## Authorization
클라이언트 인증 정보를 서버에 전달
* Authorization: Basic xxxxxxxxxxxxxxxx

## WWW-Authenticate
리소스 접근시 필요한 인증 방법 정의
* 401 Unauthorized 응답과 함께 사용
* WWW-Authenticate: Newauth realm="apps", type=1, title="Login to \"apps\"", Basic realm="simple"

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)