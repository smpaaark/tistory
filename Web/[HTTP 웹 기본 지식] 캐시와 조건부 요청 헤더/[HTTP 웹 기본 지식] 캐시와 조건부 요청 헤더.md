## 캐시 제어 헤더
* Cached-Control: 캐시 제어
* Pragma: 캐시 제어(하위 호환)
* Expires: 캐시 유효 기간(하위 호환)

### Cache-Control
캐시 지시어(directives)
* Cache-Control: max-age
  * 캐시 유효 시간, 초 단위
* Cache-Control: no-cache
  * 데이터는 캐시해도 되지만, 항상 원(origin) 서버에 검증하고 사용
* Cache-Control: no-store
  * 데이터에 민감한 정보가 있으므로 저장하면 안됨   
  (메모리에서 사용하고 최대한 빨리 삭제)

### Pragma
캐시 제어(하위 호환)
* Pragma: no-cache
* HTTP 1.0 하위 호환

### Expires
캐시 만료일 지정(하위 호환)
* expires: Mon, 01 Jan 1990 00:00:00 GMT
* 캐시 만료일을 정확한 날짜로 지정
* HTTP 1.0 부터 사용
* 지금은 더 유연한 Cache-Control: max-age 권장
* Cache-Control: max-age와 함께 사용하면 Expires는 무시

## 검증 헤더와 조건부 요청 헤더
* 검증 헤더(Validator)
  * ```ETag```: "v1.0", ```ETag```: "asid93jkrh2l"
  * ```Last-Modified```: Thu, 04 Jun 2020 07:19:24 GMT
* 조건부 요청 헤더
  * If-Match, If-None-Match: ETag 값 사용
  * If-Modified-Since, If-Unmodified-Since: Last-Modified 값 사용

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)