* Host: 요청한 호스트 정보(도메인)
* Location: 페이지 리다이렉션
* Allow: 허용 가능한 HTTP 메서드
* Retry-After: 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간

## Host
요청한 호스트 정보(도메인)
* 요청에 사용
* 필수
* 하나의 서버가 여러 도메인을 처리해야 할 때
* 하나의 IP 주소에 여러 도메인이 적용되어 있을 때

## Location
페이지 리다이렉션
* 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)
* 응답코드 3xx에서 설명
* 201(Created): Location 값은 요청에 의해 생성된 리소스 URI
* 3xx(Redirection): Location 값은 요청을 자동으로 리다이렉션하기 위한 대상 리소스를 가리킴

## Allow
허용 가능한 HTTP 메서드
* 405(Method Not Allowed) 에서 응답에 포함해야 함
* Allow: GET, HEAD, PUT

## Retry-After
유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
* 503(Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
* Retry-After: Fri, 31 Dec 1999 23:59:59 GMT(날짜 표기)
* Retry-After: 120(초단위 표기)

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)