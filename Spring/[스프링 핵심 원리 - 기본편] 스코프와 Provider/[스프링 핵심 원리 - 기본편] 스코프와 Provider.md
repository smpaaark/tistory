첫번째 해결방안은 Provider를 사용하는 것입니다.   
```
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }

}
```
```
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final ObjectProvider<MyLogger> myLoggerProvider;

    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }

}
```

```
[871922ad-4ca3-4c47-91fd-ca8db134ac15] request scope bean create:hello.core.common.MyLogger@6be579a5
[871922ad-4ca3-4c47-91fd-ca8db134ac15][http://localhost:8080/log-demo] controller test
[871922ad-4ca3-4c47-91fd-ca8db134ac15][http://localhost:8080/log-demo] service id = testId
[871922ad-4ca3-4c47-91fd-ca8db134ac15] request scope bean close:hello.core.common.MyLogger@6be579a5
```
* ```ObjectProvider``` 덕분에 ```ObjectProvider.getObject()```를 호출하는 시점까지 request scope ```빈의 생성을 지연```할 수 있습니다.
* ```ObjectProvider.getObject()```를 호출하는 시점에는 HTTP 요청이 진행중이므로 request scope 빈의 생성이 정상 처리됩니다.
* ```ObjectProvider.getObject()```를 ```LogDemoController```, ```LogDemoService```에서 각각 한번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환됩니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)