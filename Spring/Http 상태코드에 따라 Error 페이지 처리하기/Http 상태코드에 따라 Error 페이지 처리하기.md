## 기본 에러페이지
src/main/resources/templates/error.mustache

## 상태 코드에 따른 에러 페이지
src/main/resources/templates/error/{상태코드}.mustache

## 예시
### 경로
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/Http%20%EC%83%81%ED%83%9C%EC%BD%94%EB%93%9C%EC%97%90%20%EB%94%B0%EB%9D%BC%20Error%20%ED%8E%98%EC%9D%B4%EC%A7%80%20%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0/1.PNG)

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
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/Http%20%EC%83%81%ED%83%9C%EC%BD%94%EB%93%9C%EC%97%90%20%EB%94%B0%EB%9D%BC%20Error%20%ED%8E%98%EC%9D%B4%EC%A7%80%20%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0/2.PNG)
스프링 시큐리티를 적용하여 권한이 USER일 경우에만 정상 처리되고,   
GUEST일 경우에는 403처리되게 하였다.   
현재 권한이 GUEST일 경우의 예시이다.

## 참고
* [🙈[SpringBoot] 에러 페이지 처리하기🐵](https://victorydntmd.tistory.com/339)