사이드프로젝트 테스트 코드 작성중에 MediaType.APPLICATION_JSON_UTF8가 deprecated 된 것을 확인했다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/MediaType.APPLICATION_JSON_UTF8%EA%B0%80%20deprecated%20%EB%90%98%EC%97%88%EB%8B%A4/1.PNG)

스프링 부트 2.2.*부터 MediaType 중에 (UTF8)인코딩이 들어간 상수가 deprecation 되었다고 한다.   
따라서 코드를 아래와 같이 바꿔주면 된다.   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/MediaType.APPLICATION_JSON_UTF8%EA%B0%80%20deprecated%20%EB%90%98%EC%97%88%EB%8B%A4/2.PNG)
