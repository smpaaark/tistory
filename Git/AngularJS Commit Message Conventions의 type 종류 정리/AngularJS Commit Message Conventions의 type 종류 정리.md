## 커멧 메시지 헤더 (Commit Message Header)
```
커밋 메시지 헤더
<type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ 명령문, 현재 시제로 작성합니다. 대문자를 사용하지 않으며, 마침표로 끝내지 않습니다.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|dev-infra|docs-infra|migrations|
  │                          ngcc|ve
  │
  └─⫸ Commit Type: build|ci|docs|feat|fix|perf|refactor|test
The <type> and <summary> fields are mandatory, the (<scope>) field is optional.
```
* 커밋 메시지의 첫번째 줄인 커밋 메시지 헤더는 변화에 대한 간결한 설명을 포함합니다.

## \<type>에 들어갈 수 있는 항목들
* feat : 새로운 기능 추가
* fix : 버그 수정
* docs : 문서 관련
* style : 스타일 변경 (포매팅 수정, 들여쓰기 추가, …)
* refactor : 코드 리팩토링
* test : 테스트 관련 코드
* build : 빌드 관련 파일 수정
* ci : CI 설정 파일 수정
* perf : 성능 개선
* chore : 그 외 자잘한 수정

## 참조
* [[Git] 커밋 메시지 규약 정리 (the AngularJS commit conventions)](https://velog.io/@outstandingboy/Git-%EC%BB%A4%EB%B0%8B-%EB%A9%94%EC%8B%9C%EC%A7%80-%EA%B7%9C%EC%95%BD-%EC%A0%95%EB%A6%AC-the-AngularJS-commit-conventions)