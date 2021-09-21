## 웹 환경 추가
웹 스코프는 웹 환경에서만 동작하므로 web 환경이 동작하도록 라이브러리를 추가해야합니다.

### build.gradle에 추가
```
//web 라이브러리 추가
implementation 'org.springframework.boot:spring-boot-starter-web'
```

```hello.core.CoreApplication```의 main 메서드를 실행하면 웹 애플리케이션이 실행되는 것을 확인할 수 있습니다.

```
2021-09-21 19:20:53.037  INFO 21424 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-09-21 19:20:53.058  INFO 21424 --- [           main] hello.core.CoreApplication               : Started CoreApplication in 10.719 seconds (JVM running for 12.332)
```

> 참고: ```spring-boot-starter-web``` 라이브러리를 추가하면 스프링 부트는 내장 톰캣 서버를 활용해서 웹 서버와 스프링을 함께 실행시킵니다.

> 참고: 스프링 부트는 웹 라이브러리가 없으면 ```AnnotationConfigApplicationContext```을 기반으로 애플리케이션을 구동합니다. 웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로 ```AnnotationConfigServletWebServerApplicationContext```를 기반으로 애플리케이션을 구동합니다.

만약 기본 포트인 8080 포트를 다른 곳에서 사용중이어서 오류가 발생하면 포트를 변경해야 합니다.   
```main/resources/application.properteis```
```
server.port=9090
```

## request 스코프 예제 개발
동시에 여러 HTTP 요청이 오면 정확히 어떤 요청이 남긴 로그인지 구분하기 어렵습니다. 이럴때 사용하기 딱 좋은것이 바로 request 스코프입니다.

```
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```
* 기대하는 공통 포멧: [UUID][requestURL]{message}
* UUID를 사용해서 HTTP 요청을 구분합니다.
* requestURL 정보도 추가로 넣어서 어떤 URL을 요청해서 남은 로그인지 확인합니다.

## MyLogger
```
@Component
@Scope(value = "request")
public class MyLogger {

    private String uuid;
    private String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }

    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }

    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }

}
```
* 로그를 출력하기 위한 ```MyLogger``` 클래스입니다.
* ```@Scope(value = "request")```를 사용해서 request 스코프로 지정했습니다. 이제 이 빈은 HTTP 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸됩니다.
* 이 빈이 생성되는 시점에 자동으로 ```@PostConstruct``` 초기화 메서드를 사용해서 uuid를 생성해서 저장해둡니다. 이 빈은 HTTP 요청 당 하나씩 생성되므로, uuid를 저장해두면 다른 HTTP 요청과 구분할 수 있습니다.
* 이 빈이 소멸되는 시점에 ```@PreDestroy```를 사용해서 종료 메시지를 남깁니다.
* ```requestURL```은 이 빈이 생성되는 시점에는 알 수 없으므로, 외부에서 setter로 입력 받습니다.

## LogDemoController
```
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }

}
```
* HttpServletRequest를 통해서 요청 URL을 받았습니다.
  * requestURL 값 ```http://localhost:8080/log-demo```
* 이렇게 받은 requestURL 값을 myLogger에 저장해둡니다. myLogger는 HTTP 요청 당 각각 구분되므로 다른 HTTP 요청 때문에 값이 섞이는 걱정은 하지 않아도 됩니다.

> 참고: requestURL을 MyLogger에 저장하는 부분은 컨트롤러 보다는 공통 처리가 가능한 스프링 인터셉터나 서블릿 필터 같은 곳을 활용하는 것이 좋습니다.

## LogDemoService 추가
```
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final MyLogger myLogger;

    public void logic(String id) {
        myLogger.log("service id = " + id);
    }

}
```
* request scope를 사용하지 않고 파라미터로 이 모든 정보를 서비스 계층에 넘긴다면, 파라미터가 많아서 지저분해집니다. 더 문제는 reqeustURL 같은 웹과 관련된 정보가 웹과 관련없는 서비스 계층까지 넘어가게 됩니다. 웹과 관련된 부분은 컨트롤러까지만 사용해야 합니다. 서비스 계층은 웹 기술에 종속되지 않고, 가급적 순수하게 유지하는 것이 유지보수 관점에서 좋습니다.
* request scope의 MyLogger 덕분에 이런 부분을 파라미터로 넘기지 않고, MyLogger의 멤버변수에 저장해서 코드와 계층을 깔끔하게 유지할 수 있습니다.

## 기대하는 출력
```
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

## 실제는 기대와 다르게 애플리케이션 실행 시점에 오류 발생
```
Error creating bean with name 'myLogger': Scope 'request' is not active for the current thread; consider defining a scoped proxy for this bean if you intend to refer to it from a singleton;
```

스프링 애플리케이션을 실행 시키면 오류가 발생합니다. 스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성해서 주입이 가능하지만, request 스코프 빈은 아직 생성되지 않습니다. 이 빈은 실제 고객의 요청이 와야 생성할 수 있습니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)