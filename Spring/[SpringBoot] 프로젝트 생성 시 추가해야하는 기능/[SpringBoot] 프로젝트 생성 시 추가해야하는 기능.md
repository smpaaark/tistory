스프링 부트 프로젝트 생성 시 스프링 부트 스타터(https://start.spring.io/)를 자주 이용합니다.   
이때 필수로 추가해야하는 의존성은 다음과 같습니다. (제 개인적인 생각입니다😅)

## 추가해야할 의존성
* Spring Web
* Thymeleaf 또는 Mustache
* Spring Data JPA
* H2 Database
* Lombok
* Validation   
![1]()

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