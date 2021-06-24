## 기본 에러페이지
src/main/resources/templates/error.mustache

## 상태 코드에 따른 에러 페이지
src/main/resources/templates/error/{상태코드}.mustache

## 예시
### 경로
![1]()

### 페이지 코드
```
{{>layout/header}}

<body>
<div class="container">
    {{>layout/bodyHeader}}

    <p>권한이 없습니다. 관리자에게 문의해주세요.</p>
    <p>관리자 이메일: smpark911020@gmail.com</p>

{{>layout/footer}}
```

### 결과 화면
![2]()
스프링 시큐리티를 적용하여 권한이 USER일 경우에만 정상 처리되고,   
GUEST일 경우에는 403처리되게 하였다.   
현재 권한이 GUEST일 경우의 예시이다.

## 참고
* [🙈[SpringBoot] 에러 페이지 처리하기🐵](https://victorydntmd.tistory.com/339)