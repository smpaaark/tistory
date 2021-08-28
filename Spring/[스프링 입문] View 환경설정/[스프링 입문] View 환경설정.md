## Welcome Page 만들기
* 스프링 부트가 제공하는 Welcome Page 기능
  * ```static/index.html```을 올려두면 Welcome page 기능을 제공합니다.
```
<!DOCTYPE HTML>
<html>
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>
```
(static/index.html)   
![2]()   
(localhost:8080으로 접속하면 위의 html 내용이 표시됩니다.)

## 동작 환경
![1]()   
* 컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(```viewResolver```)가 화면을 찾아서 처리합니다.
  * 스프링 부트 템플릿엔진 기본 viewName 매핑
  * ```resources:templates/``` + {ViewName} + ```.html```
```
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "hello!!");

        return "hello";
    }

}
```
(HelloController 코드)

```
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>
</body>
</html>
```
(resources/templates/hello.html 코드)

![3]()   
(/hello로 접속하면 위의 html 내용이 화면에 표시됩니다.)

### 참고
```spring-boot-devtools``` 라이브러리를 추가하면, ```html``` 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능합니다.   
인텔리J 컴파일 방법: 메뉴 build -> Recompile

```
compileOnly("org.springframework.boot:spring-boot-devtools")
```
(build.gradle의 dependencies에 위 코드 추가)

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)