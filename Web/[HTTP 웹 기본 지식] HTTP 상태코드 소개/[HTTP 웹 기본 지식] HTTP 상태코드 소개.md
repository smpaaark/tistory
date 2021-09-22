## 상태 코드
클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능
* 1xx(Informational): 요청이 수신되어 처리중
* 2xx(Successful): 요청 정상 처리
* 3xx(Redirection): 요청을 완료하려면 추가 행동이 필요
* 4xx(Client Error): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음
* 5xx(Server Error): 서버 오류, 서버가 정상 요청을 처리하지 못함

## 모르는 상태 코드가 나타난 경우
* 클라이언트는 상위 상태코드로 해석해서 처리
* 미래에 새로운 상태 코드가 추가되어도 클라이언트를 변경하지 않아도 됨
* 예)
  * 299 ??? -> 2xx (Successful)
  * 451 ??? -> 4xx (Client Error)
  * 599 ??? -> 5xx (Server Error)

## 1xx(Informational)
요청이 수신되어 처리중
  * 거의 사용되지 않음

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)