## 예제 코드
```
public class NetworkClient {

    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }

    // 서비스 시작시 호출
    public void connect() {
        System.out.println("connect: " + url);
    }

    public void call(String message) {
        System.out.println("call : " + url + " message = " + message);
    }

    // 서비스 종료시 호출
    public void disconnect() {
        System.out.println("close " + url);
    }

    @PostConstruct
    public void init() {
        System.out.println("NetworkClient.init");
        connect();
        call("초기화 연결 메시지");
    }

    @PreDestroy
    public void close() {
        System.out.println("NetworkClient.close");
        disconnect();
    }

}
```
```
@Configuration
static class LifeCycleConfig {

    @Bean
    public NetworkClient networkClient() {
        NetworkClient networkClient = new NetworkClient();
        networkClient.setUrl("http://hello-spring.dev");
        return networkClient;
    }

}
```

### 실행 결과
```
생성자 호출, url = null
NetworkClient.init
connect: http://hello-spring.dev
call : http://hello-spring.dev message = 초기화 연결 메시지
23:25:31.960 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@2053d869, started on Mon Sep 20 23:25:31 KST 2021
NetworkClient.close
close http://hello-spring.dev
```

```@PostContruct```, ```@PreDestroy``` 이 두 애노테이션을 사용하면 가장 편리하게 초기화와 종료를 실행할 수 있습니다.

## \@PostConstruct, \@PreDestroy 애노테이션 특징
* 최신 스프링에서 가장 권장하는 방법입니다.
* 애노테이션 하나만 붙이면 되므로 매우 편리합니다.
* 패키지를 잘 보면 ```javax.annotation.PostConstruct```입니다. 스프링에 종속적인 기술이 아니라 JSR-250라는 자바 표준입니다. 따라서 스프링이 아닌 다른 컨테이너에서도 동작합니다.
* 컴포넌트 스캔과 잘 어울립니다.
* 유일한 단점은 외부 라이브러리에는 적용하지 못한다는 것입니다. 외부 라이브러리를 초기화, 종료 해야 하면 \@Bean의 기능을 사용해야 합니다.

## 정리
* ```@PostConstruct, @PreDestroy 애노테이션을 사용해야 합니다.```
* 코드를 고칠 수 없는 외부 라이브러리를 초기화, 종료해야 하면 ```@Bean```의 ```initMethod```, ```destroyMethod```를 사용하면 됩니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)