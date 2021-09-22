* HTTP API - 컬렉션
  * ```POST 기반 등록```
  * 예) 회원 관리 API 제공
* HTTP API - 스토어
  * ```PUT 기반 등록```
  * 예) 정적 컨텐츠 관리, 원격 파일 관리
* HTML FORM 사용
  * 웹 페이지 회원 관리
  * GET, POST만 지원

## 회원 관리 시스템
### API 설계 - POST 기반 등록
* ```회원``` 목록 /members -> ```GET```
* ```회원``` 등록 /members -> ```POST```
* ```회원``` 조회 /members/{id} -> ```GET```
* ```회원``` 수정 /members/{id} -> ```PATCH, PUT, POST```
* ```회원``` 삭제 /members/{id} -> ```DELETE```

### POST - 신규 자원 등록 특징
* 클라이언트는 등록될 리소스의 URI를 모릅니다.
  * 회원 등록 /members -> POST
  * POST /members
* 서버가 새로 등록된 리소스 URI를 생성해줍니다.
  * HTTP/1.1 201 Created   
  Location: ```/members/100```
* 컬렉션(Collection)
  * 서버가 관리하는 리소스 디렉토리
  * 서버가 리소스의 URI를 생성하고 관리
  * 여기서 컬렉션은 /members

## 파일 관리 시스템
### API 설계 - PUT 기반 등록
* ```파일``` 목록 /files -> ```GET```
* ```파일``` 조회 /files/{filename} -> ```GET```
* ```파일``` 등록 /files/{filename} -> ```PUT```
* ```파일``` 삭제 /files/{filename} -> ```DELETE```
* ```파일``` 대량 등록 /files -> ```POST```

### PUT - 신규 자원 등록 특징
* 클라이언트가 리소스 URI를 알고 있어야 합니다.
  * 파일 등록 /files/{filename} -> PUT
  * PUT ```/files/star.jpg```
* 클라이언트가 직접 리소스의 URI를 지정합니다.
* 스토어(store)
  * 클라이언트가 관리하는 리소스 저장소
  * 클라이언트가 리소스의 URI를 알고 관리
  * 여기서 스토어는 /files

## HTML FORM 사용
* HTML FORM은 ```GET, POST만 지원```
* AJAX 같은 기술을 사용해서 해결 가능 -> 회원 API 참고
* 여기서는 순수 HTML, HTML FORM 이야기
* GET, POST만 지원하므로 제약이 있음
* ```회원``` 목록 /members -> ```GET```
* ```회원``` 등록 폼 /members/new -> ```GET```
* ```회원``` 등록 /members/new, /members -> ```POST```
* ```회원``` 조회 /members/{id} -> ```GET```
* ```회원``` 수정 폼 /members/{id}/edit -> ```GET```
* ```회원``` 수정 /members/{id}/edit, /members/{id} -> ```POST```
* ```회원``` 삭제 /members/{id}/delete -> ```POST```
* HTML FORM은 ```GET, POST만 지원```
* ```컨트롤 URI```
  * GET, POST만 지원하므로 제약이 있음
  * 이런 제약을 해결하기 위해 동사로 된 리소스 경로 사용
  * POST의 /new, /edit, /delete가 컨트롤 URI
  * HTTP 메서드로 해결하기 애매한 경우 사용(HTTP API 포함)

## 정리
* HTTP API - 컬렉션
  * POST 기반 등록
  * 서버가 리소스 URI 결정
* HTTP API - 스토어
  * PUT 기반 등록
  * 클라이언트가 리소스 URI 결정
* HTML FORM 사용
  * 순수 HTML + HTML form 사용
  * GET, POST만 지원

## 참고하면 좋은 URI 설계 개념
* 문서(document)
  * 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
  * 예) /members/100, /files/star.jpg
* 컬렉션(collection)
  * 서버가 관리하는 리소스 디렉터리
  * 서버가 리소스의 URI를 생성하고 관리
  * 예) /members
* 스토어(store)
  * 클라이언트가 관리하는 자원 저장소
  * 클라이언트가 리소스의 URI를 알고 관리
  * 예) files
* 컨트롤러(controller), 컨트롤 URI
  * 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
  * 동사를 직접 사용
  * 예) /members/{id}/delete

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)