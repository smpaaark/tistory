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
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%208%20-%20%EC%84%B8%EC%85%98%20%EC%A0%80%EC%9E%A5%EC%86%8C%EB%A1%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/1.PNG)

### 세션 데이터 확인
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%208%20-%20%EC%84%B8%EC%85%98%20%EC%A0%80%EC%9E%A5%EC%86%8C%EB%A1%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/2.PNG)   

이제 서버 재시작해도 세션이 풀리지 않습니다.   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)