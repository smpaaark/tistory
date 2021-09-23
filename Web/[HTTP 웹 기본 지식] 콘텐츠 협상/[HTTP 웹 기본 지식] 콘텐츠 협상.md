## 협상(콘텐츠 네고시에이션)
클라이언트가 선호하는 표현 요청
* Accept: 클라이언트가 선호하는 미디어 타입 전달
* Accept-Charset: 클라이언트가 선호하는 문자 인코딩
* Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
* Accept-Language: 클라이언트가 선호하는 자연 언어
* 협상 헤더는 요청시에만 사용

## 협상과 우선순위1
Quality Values(q)
* Quality Values(q) 값 사용
* 0 ~ 1, ```클수록 높은 우선순위```
* 생략하면 1
* Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en:q=0.7
  * 1. ko-KR;q=1 (q생략)
  * 2. ko;q=0.9
  * 3. en-US;q=0.8
  * 4. en;q=0.7

## 협상과 우선순위2
Quality Values(q)
* 구체적인 것이 우선합니다.
* Accept: ```text/*, text/plain, text/plain;format=flowed, */*```
  * 1. text/plain,format=flowed
  * 2. text/plain
  * 3. text/*
  * 4. \*/\*

## 협상과 우선순위3
Quality Values(q)
* 구체적인 것을 기준으로 미디어 타입을 맞춥니다.
* Accept: ```text/*```;q=0.3, ```text/html```;q=0.7, ```text/html;level=1```,   
```text/html;level=2```;q=0.4, ```*/*```;q=0.5

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)