## AWS 보안 그룹 변경
먼저 EC2에 스프링 부트 프로젝트가 8080 포트로 배포되었으니, 8080 포트가 보안 그룹에 열려 있는지 확인합니다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/1.PNG)

8080이 열려 있다면 OK, 안 되어있다면 [Edit inbound rules] 버튼을 눌러 추가해 줍니다.   

## AWS EC2 도메인으로 접속
왼쪽 사이드바의 [인스턴스] 메뉴를 클릭합니다.   
본인이 생성한 EC2 인스턴스를 선택하면 다음과 같이 상세 정보에서 퍼블릭 DNS를 확인할 수 있습니다.   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/2.PNG)   

이 주소가 EC2에 자동으로 할당된 도메인입니다.   
인터넷이 되는 장소 어디나 이 주소를 입력하면 우리 EC2 서버에 접근할 수 있습니다.   

자 그럼 이제 도메인 주소에 8080포트를 붙여 브라우저에 입력합니다.   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/3.PNG)   

현재 상태에서는 해당 서비스에 EC2의 도메인을 등록하지 않았기 때문에 구글과 네이버 로그인이 작동하지 않습니다.   

그래서 차례로 서비스에 등록하겠습니다.   

## 구글에 EC2 주소 등록
[구글 웹 콘솔](https://console.cloud.google.com/home/dashboard)로 접속하여 본인의 프로젝트로 이동한 다음 [API 및 서비스 => 사용자 인증 정보]로 이동합니다.   
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/4.PNG)   

[OAuth 등의 화면] 탭을 선택하고 아래에서 승인된 도메인에 'http://' 없이 EC2의 퍼블릭 DNS를 등록합니다.   
![5](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/5.PNG)   
![6](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/6.PNG)   

[사용자 인증 정보] 탭을 클릭해서 본인이 등록한 서비스의 이름을 클릭합니다.   
![7](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/7.PNG)   

퍼블릭 DNS 주소에 :8080/login/oauth2/code/google 주소를 추가하여 승인된 리디렉션 URI에 등록합니다.   
![8](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/8.PNG)   

EC2 DNS 주소로 이동해서 다시 구글 로그인을 시도해 보면 다음과 같이 로그인이 정상적으로 수행되는 것을 확인할 수 있습니다.   
![9](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/9.PNG)   

## 네이버에 EC2 주소 등록
[네이버 개발자 센터](https://developers.naver.com/apps/#/myapps)로 접속해서 본인의 프로젝트로 이동합니다.   
![10](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/10.PNG)   

아래로 내려가 보면 PC 웹 항목이 있는데 여기서 서비스 URL과 Callback URL 2개를 수정합니다.   
![11](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/11.PNG)   
1. 서비스 URL
  * 로그인을 시도하는 서비스가 네이버에 등록된 서비스인지 판단하기 위한 항목입니다.   
  * 8080 포트는 제외하고 실제 도메인 주소만 입력합니다.
  * 네이버에서 아직 지원되지 않아 하나만 등록 가능합니다.
  * 즉, EC2의 주소를 등록하면 localhost가 안 됩니다.
  * 개발 단계에서는 등록하지 않는 것을 추천합니다.
  * localhost도 테스트하고 싶으면 네이버 서비스를 하나 더 생성해서 키를 발급받으면 됩니다.   
2. Callback URL
  * 전체 주소를 등록합니다(EC2 퍼블릭 DNS:8080/login/oauth2/code/naver).

2개 항목을 모두 수정/추가하였다면 구글과 마찬가지로 네이버 로그인을 시도합니다.   
그럼 다음과 같이 로그인이 정상적으로 수행되는 것을 확인할 수 있습니다.   
![12](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20EC2%EC%97%90%EC%84%9C%20%EC%86%8C%EC%85%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0/12.PNG)   

구글과 네이버 로그인도 EC2와 연동 완료되었습니다.   

하지만 현재 방식은 몇 가지 문제가 있습니다.   
* 수동 실행되는 Test
  * 본인이 짠 코드가 다른 개발자의 코드에 영향을 끼치지 않는지 확인하기 위해 전체 테스트를 수행해야 합니다.
  * 현재 상태에선 항상 개발자가 작업을 진행할 때마다 수동으로 전체 테스트를 수행해야만 합니다.
* 수동 Build
  * 다른 사람이 작성한 브랜치와 본인이 작성한 브랜치가 합쳐졌을 때(Merge) 이상이 없는지는 Build를 수행해야만 알 수 있습니다.
  * 이를 매번 개발자가 직접 실행해봐야 합니다.   

그래서 다음 글에서는 이런 수동 Test & Build를 자동화시키는 작업을 진행하겠습니다.   

깃허브에 푸시를 하면 자동으로 Test & Build & Deploy가 진행되도록 개선하는 작업입니다.   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)