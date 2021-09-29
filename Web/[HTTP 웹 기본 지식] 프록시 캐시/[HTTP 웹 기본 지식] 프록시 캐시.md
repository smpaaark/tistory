## Cache-Control
캐시 지시어(directives) - 기타
* Cache-Control: public
  * 응답이 public 캐시에 저장되어도 됨
* Cache-Control: private
  * 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야 함(기본값)
* Cache-Control: s-maxage
  * 프록시 캐시에만 적용되는 max-age
* Age: 60 (HTTP 헤더)
  * 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)