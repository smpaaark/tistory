사이드 프로젝트 진행 중에 웹 계층을 연결하면서 Error 페이지 처리가 필요한 상황이 생겼다.   
상황은 차량 매입 목록에서 차량을 하나 클릭 했을 때 상세 및 수정 페이지로 이동하는데   
이 때 클릭한 차량의 id가 이미 삭제되었을 경우 Error 페이지로 이동시켜줘야 했다.   

이럴때는 ErrorController를 하나 생성한 후 이 컨트롤러를 상속받으면 된다.
간단하게 에러메시지를 모델에 담은 후 error 페이지로 리턴시켰다.   
## ErrorController
```
@Controller
@Slf4j
public class ErrorController {

    @ExceptionHandler(Exception.class)
    public String exceptionHandler(Model model, Exception e) {
        log.info("\n\n=== Exception ===\n* message: " + e.getMessage());
        model.addAttribute("message", e.getMessage());

        return "error";
    }

    @ExceptionHandler(NotFoundCarException.class)
    public String notFoundCarHandler(Model model, NotFoundCarException e) {
        log.info("\n\n=== NotFoundCarException ===\n* message: " + e.getMessage());
        model.addAttribute("message", e.getMessage());

        return "error";
    }

}
```

## Error.mustache
```
{{>layout/header}}

<body>
<div class="container">
    {{>layout/bodyHeader}}

    <p>{{message}}</p>
    <p>[홈으로] 버튼을 눌러 홈으로 돌아가주세요.</p>

{{>layout/footer}}
```

## IndexController
```
@Controller
@RequiredArgsConstructor
@Slf4j
public class IndexController extends ErrorController {
```


## 결과
![1]()