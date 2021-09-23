* Content-Type: 표현 데이터의 형식
* Content-Encoding: 표현 데이터의 압축 방식
* Content-Language: 표현 데이터의 자연 언어
* Content-Length: 표현 데이터의 길이
* 표현 헤더는 전송, 응답 둘다 사용

## Content-Type
표현 데이터의 형식 설명
* 미디어 타입, 문자 인코딩
* 예)
  * text/html; charset=utf-8
  * application/json
  * image/png

## Content-Encoding
표현 데이터 인코딩
* 표현 데이터를 압축하기 위해 사용
* 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
* 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
* 예)
  * gzip
  * deflate
  * identity

## Content-Language
표현 데이터의 자연 언어
* 표현 데이터의 자연 언어를 표현
* 예)
  * ko
  * en
  * en-US

## Content-Length
표현 데이터의 길이
* 바이트 단위
* Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)