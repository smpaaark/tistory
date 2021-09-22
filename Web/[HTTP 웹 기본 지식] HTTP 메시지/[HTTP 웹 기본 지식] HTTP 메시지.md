## 모든 것이 HTTP
HTTP 메시지에 모든 것을 전송
* HTML, TEXT
* IMAGE, 음성, 영상, 파일
* JSON, XML
* 거의 모든 형태의 데이터 전송 가능
* 서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용
* ```지금은 HTTP 시대```

## 시작 라인
### 요청 메시지
```
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```
* start-line = ```request-line``` / status-line
* ```request-line``` = method SP(공백) reqeust-target SP HTTP-version CRLF(엔터)
* HTTP 메서드 (GET: 조회)
* 요청 대상(/search?q=hello&hl=ko)
* HTTP Version

### 요청 메시지 - HTTP 메서드
* 종류: GET, POST, PUT, DELETE...
* 서버가 수행해야 할 동작 지정
  * GET: 리소스 조회
  * POST: 요청 내역 처리

### 요청 메시지 - 요청 대상
* absolute-path\[?query] (절대경로[?쿼리])
* 절대경로= "/"로 시작하는 경로
* 참고: *, http://...?x=y와 같이 다른 유형의 경로지정 방법도 있습니다.

### 요청 메시지 - HTTP 버전
* HTTP Version

### 응답 메시지
```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

<html>
  <body>...</body>
</html>
```
* start-line = request-line / ```status-line```
* ```status-line``` = HTTP-version SP status-code SP reason-phrase CRLF
* HTTP 버전
* HTTP 상태 코드: 요청 성공, 실패를 나타냄
  * 200: 성공
  * 400: 클라이언트 요청 오류
  * 500: 서버 내부 오류
* 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글

## HTTP 헤더
* header-field = field-name ":" OWS field-value OWS (OWS:띄어쓰기 허용)
* field-name은 대소문자 구문 없음
```
...
Host: www.google.com
```
```
...
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

...
```

### 용도
* HTTP 전송에 필요한 모든 부가정보
* 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보...
* 표준 헤더가 너무 많음
* 필요시 임의의 헤더 추가 가능
  * helloworld: hihi

## HTTP 메시지 바디
```
...

<html>
  <body>...</body>
</html>
```
### 용도
* 실제 전송할 데이터
* HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

## 단순함 확장 가능
* HTTP는 단순합니다.
* HTTP 메시지도 매우 단순합니다.
* 크게 성공하는 표준 기술은 단순하지만 확장 가능한 기술

## HTTP 정리
* HTTP 메시지에 모든 것을 전송
* HTTP 역사 HTTP/1.1을 기준으로 학습
* 클라이언트 서버 구조
* 무상태 프로토콜(스테이스리스)
* HTTP 메시지
* 단순함, 확장 가능
* ```지금은 HTTP 시대```

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)