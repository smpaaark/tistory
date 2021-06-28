spring.session.jdbc.initialize-schema 옵션의 default값은 ```embeded``` 입니다.   
현재 프로젝트에서 사용하는 데이터베이스가 embeded 데이터베이스가 아니라면
* SPRING_SESSION
* SPRING_SESSION_ATTRIBUTES   

이 두 테이블이 자동 생성 되지 않습니다.

### 서버 구동 후 테이블 상태
![1]()   
SPRING_SESSION, SPRING_SESSION_ATTRIBUTES 테이블이 자동 생성되지 않은 것을 확인할 수 있습니다.

## application.properties
```
spring.session.jdbc.initialize-schema=always
```
application.properties에 위 코드를 추가하게 되면    
SPRING_SESSION, SPRING_SESSION_ATTRIBUTES 테이블이 자동 생성됩니다.   
![2]()

## 참고
* [Spring Session with JDBC](https://www.javadevjournal.com/spring/spring-session-with-jdbc/)