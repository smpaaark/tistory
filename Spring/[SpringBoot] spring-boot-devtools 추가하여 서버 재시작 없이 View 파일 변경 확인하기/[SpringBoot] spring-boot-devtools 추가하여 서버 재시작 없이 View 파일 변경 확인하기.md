스프링부트를 이용하여 웹 개발을 하다보면 매우 자주 view 파일을 수정하게 된다.   
수정할 때마다 서버를 재시작해야 변경된 내용이 반영되기 때문에 매우매우매우 시간낭비이고 짜증난다.   
하지만 ```spring-boot-devtools``` 이놈을 추가하면 서버 재시작 없이 View 파일 변경이 가능하다.

## 의존성 추가
저는 maven을 사용하기 때문에 pom.xml에 추가했습니다.
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

## 테스트
1. 현재 화면   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringBoot%5D%20spring-boot-devtools%20%EC%B6%94%EA%B0%80%ED%95%98%EC%97%AC%20%EC%84%9C%EB%B2%84%20%EC%9E%AC%EC%8B%9C%EC%9E%91%20%EC%97%86%EC%9D%B4%20View%20%ED%8C%8C%EC%9D%BC%20%EB%B3%80%EA%B2%BD%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0/1.PNG)

2. html 파일 수정
```
<body>
헬로우~~~~~
<a href="/hello">hello</a>
```

3. Build -> Recompile 'html파일명'
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringBoot%5D%20spring-boot-devtools%20%EC%B6%94%EA%B0%80%ED%95%98%EC%97%AC%20%EC%84%9C%EB%B2%84%20%EC%9E%AC%EC%8B%9C%EC%9E%91%20%EC%97%86%EC%9D%B4%20View%20%ED%8C%8C%EC%9D%BC%20%EB%B3%80%EA%B2%BD%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0/2.PNG)

4. 브라우저 새로고침   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringBoot%5D%20spring-boot-devtools%20%EC%B6%94%EA%B0%80%ED%95%98%EC%97%AC%20%EC%84%9C%EB%B2%84%20%EC%9E%AC%EC%8B%9C%EC%9E%91%20%EC%97%86%EC%9D%B4%20View%20%ED%8C%8C%EC%9D%BC%20%EB%B3%80%EA%B2%BD%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0/3.PNG)

> 와우!