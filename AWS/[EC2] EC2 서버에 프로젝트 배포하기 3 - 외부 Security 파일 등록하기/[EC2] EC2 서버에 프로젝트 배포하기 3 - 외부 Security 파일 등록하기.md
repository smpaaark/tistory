ClientRegistrationRepository를 생성하려면 clientId와 clientSecret이 필수입니다.   
로컬 PC에서 실행할 때는 application-oauth.properties가 있어서 문제가 없었습니다.   

하지만 이 파일은 .gitignore로 git에서 제외 대상 처리해서 깃허브에는 올라가 있지 않습니다.   

애플리케이션을 실행하기 위해 공개된 저장소에 ClientId와 ClientSecret을 올릴 수는 없으니 서버에서 직접 이 설정들을 가지고 있게 하겠습니다.   
> Travis CI는 깃허브의 비공개된 저장소를 사용할 경우 비용이 부과됩니다.

먼저 step1이 아닌 app 디렉토리에 properties 파일을 생성합니다.   
```
vim /home/ec2-user/app/application-oauth.properties
```

그리고 로컬에 있는 application-oauth.properties 파일 내용을 그대로 붙여넣기를 합니다.   
해당 파일을 저장하고 종료합니다(:wq).   
그리고 방금 생성한 application-oauth.properties를 쓰도록 deploy.sh 파일을 수정합니다.
```
nohup java -jar \
        -Dspring.config.location=classpath:/application.properties,/home/ec2-user/app/application-oauth.properties \
        $REPOSITORY/$JAR_NAME 2>&1 &
```
1. -Dspring.config.location
  * 스프링 설정 파일 위치를 지정합니다.
  * 기본 옵션들을 담고 있는 application.properties와 OAuth 설정들을 담고 있는 application-oauth.properties의 위치를 지정합니다.
  * classpath가 붙으면 jar 안에 있는 resources 디렉토리를 기준으로 경로가 생성됩니다.
  * application-oauth.properties는 절대경로를 사용합니다.   
    * 외부에 파일이 있기 때문입니다.    

수정이 다 되었다면 다시 deploy.sh를 실행해 봅니다.   
그럼 다음과 같이 정상적으로 실행된 것을 확인할 수 있습니다.    
![1]()

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)