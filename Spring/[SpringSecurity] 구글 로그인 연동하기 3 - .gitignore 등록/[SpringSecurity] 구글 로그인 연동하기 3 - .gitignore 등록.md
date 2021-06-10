구글 로그인을 위한 클라이언트 ID(clientId)와 클라이언트 보안 비밀은 보안이 중요한 정보들이다.   
이들이 외부에 노출될 경우 언제든 개인 정보를 가져갈 수 있는 취약점이 될 수 있다.   

따라서 깃허브와 연동하여 사용한다면 application-oauth.properties 파일이 ``깃허브에 올라갈 수 있다``.   

보안을 위해 깃허브에 application-oauth.properties 파일이 올라가는 것을 방지해야 한다.   

.gitignore에 다음과 같이 한 줄의 코드를 추가한다.
```
application-oauth.properties
```

추가한 뒤 커밋했을 때 ``커밋 파일 목록에 application-oauth.properties가 나오지 않으면`` 성공이다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Spring/%5BSpringSecurity%5D%20%EA%B5%AC%EA%B8%80%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0%203%20-%20.gitignore%20%EB%93%B1%EB%A1%9D/1.PNG)

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)