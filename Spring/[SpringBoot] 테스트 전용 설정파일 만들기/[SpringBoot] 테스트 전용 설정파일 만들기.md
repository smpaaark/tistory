스프링 부트를 사용하면 application.propertes나 application.yml 파일을 사용하여 여러가지 설정을 한다.   
보통 테스트 코드에서는 내장 DB를 사용하는 것이 좋기 때문에 서버를 띄울때의 DB 설정과 테스트 코드가 실행될 때의 DB 설정을 따로 설정 할 수가 있다.   
이 밖에도 여러가지 설정들을 구분하여 설정할 수가 있다.   
* src/main/resources/application.properties: 서버 구동 시 읽는 설정 파일
* src/test/resources/application.properties: 테스트 코드 실행 시 읽는 설정 파일

## 디렉토리 구조
![1]()