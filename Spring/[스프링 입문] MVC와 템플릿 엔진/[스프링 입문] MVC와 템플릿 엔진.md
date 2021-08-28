* 서버에서 무언가를 하는 거 없이 그냥 파일을 그대로 웹 브라우저에 내려주는 것입니다.
* resources/static/ 경로의 파일들이 정적 컨텐츠 파일입니다.
  * ex) Welcome Page
```
<!DOCTYPE HTML>
<html>
<head>
    <title>static content</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
정적 컨텐츠 입니다.
</body>
</html>
```
(hello-static.html)

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20%EC%A0%95%EC%A0%81%20%EC%BB%A8%ED%85%90%EC%B8%A0/2.PNG)   
(resources/static/hello-static.html)

![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20%EC%A0%95%EC%A0%81%20%EC%BB%A8%ED%85%90%EC%B8%A0/3.PNG)   
(접속 화면)

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5B%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9E%85%EB%AC%B8%5D%20%EC%A0%95%EC%A0%81%20%EC%BB%A8%ED%85%90%EC%B8%A0/1.PNG)   
(해당하는 컨트롤러가 없으면 정적 컨텐츠에서 파일을 찾아서 브라우저에 전달합니다.)

## 참조
* [스프링 입문-코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)