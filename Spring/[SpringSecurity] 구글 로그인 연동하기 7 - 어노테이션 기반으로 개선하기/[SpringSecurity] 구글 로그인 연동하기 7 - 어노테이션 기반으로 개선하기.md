```
SessionUser user = (SessionUser) httpSession.getAttribute("user");
```
IndexController에 위의 세션값을 가져오는 코드가 많이 중복됩니다.
그래서 이 부분을 메소드 인자로 세션값을 바로 받을 수 있도록 변경해 보겠습니다.   

## \@LoginUser 어노테이션 생성
```
package com.usedcar.admin.config.auth;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.PARAMETER) //
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginUser { //
}
```
\@Target(ElementType.PARAMETER)
* 이 어노테이션이 생성될 수 있는 위치를 지정합니다.
* PARAMETER로 지정했으니 메소드의 파라미터로 선언된 객체에서만 사용할 수 있습니다.

\@interface
* 이 파일을 어노테이션 클래스로 지정합니다.
* LoginUser라는 이름을 가진 어노테이션이 생성되었다고 보면 됩니다.

## LoginUserArgumentResolver 클래스 생성
HandlerMethodArgumentResolver 인터페이스를 구현한 클래스입니다.   
HandlerMethodArgumentResolver는 한 가지 기능을 지원합니다.   
바로 조건에 맞는 경우 메소드가 있다면 HandlerMethodArgumentResolver의 구현체가 지정한 값으로 해당 메소드의 파라미터로 넘길 수 있습니다.
```
package com.usedcar.admin.config.auth;

import com.usedcar.admin.config.auth.dto.SessionUser;
import lombok.RequiredArgsConstructor;
import org.springframework.core.MethodParameter;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

import javax.servlet.http.HttpSession;

@RequiredArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {

    private final HttpSession httpSession;

    @Override
    public boolean supportsParameter(MethodParameter parameter) { //
        boolean isLoginUserAnnotation = parameter.getParameterAnnotation(LoginUser.class) != null;
        boolean isUserClass = SessionUser.class.equals(parameter.getParameterType());

        return isLoginUserAnnotation && isUserClass;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
                      NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception { //
        return httpSession.getAttribute("user");
    }
    
}
```
supportsParameter
* 컨트롤러 메서드의 특정 파라미터를 지원하는지 판단합니다.
* 여기서는 파라미터에 \@LoginUser 어노테이션이 붙어 있고, 파라미터 클래스 타입이 SessionUser.class인 경우 true를 반환합니다.

resolveArgument
* 파라미터에 전달할 객체를 생성합니다.
* 여기서는 세션에서 객체를 가져옵니다.

## WebConfig 클래스 생성
LoginUserArgumentResolver가 스프링에서 인식될 수 있도록 해주는 클래스
```
package com.usedcar.admin.config;

import com.usedcar.admin.config.auth.LoginUserArgumentResolver;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@RequiredArgsConstructor
@Configuration
public class WebConfig implements WebMvcConfigurer {

    private final LoginUserArgumentResolver loginUserArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
        argumentResolvers.add(loginUserArgumentResolver);
    }
}
```
HandlerMethodArgumentResolver는 항상 WebMvcConfigurer의 addArgumentResolvers()를 통해 추가해야 합니다.   
다른 HandlerMethodArgumentResolver가 필요하다면 같은 방식으로 추가해 주면 됩니다.

## IndexController 반복 코드 -> \@LoginUser로 수정
```
@GetMapping("/")
public String index(Model model, @LoginUser SessionUser user) { //
    log.info("\n\n=== index start ===");
    
    if (user != null) {
        model.addAttribute("name", user.getName());
    }

    log.info("\n\n=== index end ===");
    return "index";
}
```
\@LoginUser SessionUser user
* 기존에 (User) httpSession.getAttribute("user")로 가져오던 세션 정보 값이 개선되었습니다.
* 이제는 어느 컨트롤러든지 \@LoginUser만 사용하면 세션 정보를 가져올 수 있게 되었습니다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)