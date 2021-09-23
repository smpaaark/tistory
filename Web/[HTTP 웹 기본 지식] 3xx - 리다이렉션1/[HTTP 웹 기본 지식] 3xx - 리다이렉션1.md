## 3xx (Redirection)
요청을 완료하기 위해 유저 에이전트의 추가 조치 필요
* 300 Multiple Choices
* 301 Moved Permanently
* 302 Found
* 303 See Other
* 304 Not Modified
* 307 Temporary Redirect
* 308 Permanent Redirect

## 리다이렉션 이해
* 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)

### 종류
* 영구 리다이렉션 - 특정 리소스의 URI가 영구적으로 이동
  * 예) /members -> /users
  * 예) /event -> /new-event
* 일시 리다이렉션 - 일시적인 변경
  * 주문 완료 후 주문 내역 화면으로 이동
  * PRG: Post/Redirect/Get
* 특수 리다이렉션
  * 결과 대신 캐시를 사용

## 영구 리다이렉션
301, 308
* 리소스의 URI가 영구적으로 이동
* 원래의 URL를 사용X, 검색 엔진 등에서도 변경 인지
* ```301 Moved Permanently```
  * ```리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음(MAY)```
* ```308 Permanent Redirect```
  * 301과 기능은 같음
  * ```리다이렉트시 요청 메서드와 본문 유지(처음 POST를 보내면 리다이렉트도 POST 유지)```

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)