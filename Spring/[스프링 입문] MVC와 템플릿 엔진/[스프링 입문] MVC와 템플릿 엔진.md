* MVC: Model, View, Controller

```
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "spring!!");

        return "hello";
    }

    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }

}
```
(HelloController.java)

```
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```
(resources/templates/hello-template.html)   
* p 태그 안의 문자열은 서버 구동 없이 직접 파일만 실행할 때 표시되는 문자열입니다.

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20MVC%EC%99%80%20%ED%85%9C%ED%94%8C%EB%A6%BF%20%EC%97%94%EC%A7%84/2.PNG)   
(접속 화면)

## MVC, 템플릿 엔진 이미지
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20MVC%EC%99%80%20%ED%85%9C%ED%94%8C%EB%A6%BF%20%EC%97%94%EC%A7%84/1.PNG)

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)