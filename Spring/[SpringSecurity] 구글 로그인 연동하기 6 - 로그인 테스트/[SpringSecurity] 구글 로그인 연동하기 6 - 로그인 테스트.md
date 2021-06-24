## 로그인 버튼 추가
```
{{>layout/header}}

<body>
<div class="container">
    {{>layout/bodyHeader}}
    <div class="jumbotron">
        <h1>🚘중고차 관리 프로그램🚖</h1>
        {{#name}}
            <p class="lead">차량 매입 기능</p>
            <p>
                <a class="btn btn-lg btn-dark" href="/car/save">차량 매입</a>
                <a class="btn btn-lg btn-dark" href="/car/findAll">차량 목록</a>
            </p>
            <p class="lead">차량 출고 기능</p>
            <p>
                <a class="btn btn-lg btn-info" href="/car/findNormal">차량 출고</a>
                <a class="btn btn-lg btn-info" href="/release/findAll">출고 목록</a>
            </p>
        {{/name}}
        {{^name}}
            <p class="lead">🔐Login</p>
            <p>
                <a href="/oauth2/authorization/google" class="btn btn-lg btn-primary">Google Login</a>
<!--                <a href="/oauth2/authorization/naver" class="btn btn-lg btn-success">Naver Login</a>-->
            </p>
        {{/name}}
    </div>

{{>layout/footer}}
```
a href="/oauth2/authorization/google"
* 스프링 시큐리티에서 기본적으로 제공하는 로그인 URL 입니다.
* 로그아웃 URL과 마찬가지로 개발자가 별도의 컨트롤러를 생성할 필요가 없습니다.

## bodyHeader.mustache에 로그인 정보 추가
```
<div class="header">
    <a href="/"><h3 class="text-muted">홈으로</h3></a>
    {{#name}}
        <div class="form-inline">
            <h5 class="text-primary">[사용자 이름: {{name}}]</h5>
            <a href="/logout"><h5 class="text-muted">[🖱Logout]</h5></a>
        </div>
    {{/name}}
</div>
```
a href="/logout"
* 스프링 시큐리티에서 기본적으로 제공하는 로그아웃 URL입니다.
* 즉, 개발자가 별도로 저 URL에 해당하는 컨트롤러를 만들 필요가 없습니다.   
* SecurityConfig 클래스에서 URL을 변경할 수 있습니다.

## IndexController에서 userName을 model에 저장하는 코드 추가
```
@Controller
@RequiredArgsConstructor
@Slf4j
public class IndexController extends ErrorController {

    private final CarService carService;
    private final ReleaseService releaseService;
    private final HttpSession httpSession;

    @GetMapping("/")
    public String index(Model model) {
        log.info("\n\n=== index start ===");

        SessionUser user = (SessionUser) httpSession.getAttribute("user");
        if (user != null) {
            model.addAttribute("userName", user.getName());
        }
        
        log.info("\n\n=== index end ===");
        return "index";
    }

    ...

}
```
(SessionUser) httpSession.getAttribute("user")
* 앞서 작성된 CustomOAuth2UserService에서 로그인 성공 시 세션에 SessionUser를 저장하도록 구성했습니다.
* 즉, 로그인 성공 시 httpSession.getAttribute("user")에서 값을 가져올 수 있습니다.

if (user != null)
* 세션에 저장된 값이 있을 때만 model에 userName으로 등록합니다.
* 세션에 저장된 값이 없으면 model엔 아무런 값이 없는 상태이니 로그인 버튼이 보이게 됩니다.

## 로그인 화면
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/1.PNG)

## 구글 로그인 동의 화면
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/2.PNG)

## 구글 로그인 성공 후 화면
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/3.PNG)

## User 테이블 확인
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/4.PNG)

## 403(권한 거부) 에러 페이지 확인
![5](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/5.PNG)
차량 매입 페이지는 USER 권한만 접속 가능하게 처리되게 했습니다.   

## USER로 권한 변경 후 확인
![6](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/6.PNG)
현재 권한 USER로 변경

![7](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%206%20-%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%ED%85%8C%EC%8A%A4%ED%8A%B8/7.PNG)
차량 매입 화면 정상 진입 성공

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)