기본적으로 세션은 실행되는 WAS(Web Application Server)의 메모리에 저장되고 호출됩니다.   
메모리에 저장되다 보니 내장 톰캣처럼 애플리케이션 실행 시 실행되는 구조에선 항상 초기화됩니다.   

즉, 배포할 때마다 톰캣이 재시작되는 것입니다.   

이 외에도 한 가지 문제가 더 있습니다.   
2대 이상의 서버에서 서비스하고 있다면 톰캣마다 세션 동기화 설정을 해야만 합니다.   

여기서는 데이터베이스를 세션 저장소로 사용하는 방식을 선택하여 진행하겠습니다.   
> 규모가 큰 B2C 서비스에서는 Redis, Memcached와 같은 메모리 DB를 세션 저장소로 사용합니다.   
> 실제 서비스로 사용하기 위해서는 Embedded Redis와 같은 방식이 아닌 외부 메모리 서버가 필요합니다.

## spring-session-jdbc 등록
### pom.xml
```
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-jdbc</artifactId>
</dependency>
```

### application.properties
```
spring.session.store-type=jdbc
spring.session.jdbc.initialize-schema=always
```

### 테이블 생성 확인
![1]()

### 세션 데이터 확인
![2]()   

이제 서버 재시작해도 세션이 풀리지 않습니다.   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)