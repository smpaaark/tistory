스프링 부트 프로젝트 생성 시 [스프링 부트 스타터](https://start.spring.io/)를 자주 이용합니다.   
이때 필수로 추가해야하는 의존성은 다음과 같습니다. (제 개인적인 생각입니다😅)

## 추가해야할 의존성
* Spring Web
* Thymeleaf 또는 Mustache
* Spring Data JPA
* H2 Database
* Lombok
* Validation   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringBoot%5D%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EC%B6%94%EA%B0%80%ED%95%B4%EC%95%BC%ED%95%98%EB%8A%94%20%EA%B8%B0%EB%8A%A5/1.PNG)

## pom.xml
프로젝트를 import하면 아래와 같이 pom.xml에 의존성이 자동으로 추가됩니다.(저는 maven을 사용했습니다.)
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```