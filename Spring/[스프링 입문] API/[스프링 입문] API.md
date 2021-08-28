## \@ResponseBody 문자 변환
* ```@ResponseBody```를 사용하면 뷰 리졸버(```viewResolver```)를 사용하지 않습니다.
* 대신에 HTTP의 BODY에 문자 내용을 직접 반환(HTML BODY TAG를 말하는 것이 아닙니다.)
```
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {

    ...

    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name;
    }

}
```
(HelloController.java)

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20API/2.PNG)   
(문자열 그대로 리턴됩니다.)

## \@ResponseBody 객체 변환
* ```@ResponseBody```를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됩니다.
```
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {

    ...

    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }

    static class Hello {
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

    }

}
```
(HelloController.java)

![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20API/3.PNG)   
(JSON 형태로 리턴합니다.)
* {key:value}로 이루어진 구조입니다.

## \@ResponseBody 사용 원리
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20API/1.PNG)   
* ```@ResponseBody```를 사용
  * HTTP의 BODY에 문자 내용을 직접 반환
  * ```viewResolver``` 대신에 ```HttpMessageConverter```가 동작
  * 기본 문자처리: ```StringHttpMessageConverter```
  * 기본 객체처리: ```MappingJackson2HttpMessageConverter```
  * byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있습니다.

> 참고: 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 ```HttpMessageConverter```가 선택됩니다.

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)