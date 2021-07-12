깃허브에서 코드를 받아올 수 있게 EC2에 깃을 설치하겠습니다.   
EC2로 접속해서 다음과 같이 명렁어를 입력합니다.
```
sudo yum install git
```

설치가 완료되면 다음 명령어로 설치 상태를 확인합니다.
```
git --version
```

깃이 성공적으로 설치되면 git clone으로 프로젝트를 저장할 디렉토리를 생성하겠습니다.
```
mkdir ~/app && mkdir ~/app/step1
```

생성된 디렉토리로 이동합니다.
```
cd ~/app/step1
```

본인의 깃허브 웹페이지에서 https 주소를 복사합니다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%201%20-%20EC2%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20Clone%20%EB%B0%9B%EA%B8%B0/1.PNG)   

복사한 https 주소를 통해 git clone을 진행합니다.
```
git clone 복사한 주소
```

다음과 같이 클론이 진행되는 것을 볼 수 있습니다.   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%201%20-%20EC2%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20Clone%20%EB%B0%9B%EA%B8%B0/2.PNG)   

git clone이 끝났으면 클론된 프로젝트로 이동해서 파일들이 잘 복사되었는지 확인합니다.   
```
cd 프로젝트명
ll
```

다음과 같이 프로젝트의 코드들이 모두 있으면 됩니다.   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%201%20-%20EC2%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20Clone%20%EB%B0%9B%EA%B8%B0/3.PNG)   

먼저 프로젝트에 maven wrapper가 설치되어 있어야 합니다.   
아래 블로그를 참고합니다.   
[[AWS] 서버에 프로젝트 배포하기 - 무중단 배포 3](https://jinny-1st.tistory.com/29)

여기서 설치 작업을 했다면 설치된 파일들을 git push 하고 ec2서버에서 git pull로 내려받아야 합니다.   

mvw 설치가 완료되었다면 코드들이 잘 수행되는지 테스트로 검증하겠습니다.   
```
./mvnw test
```

만약 mvnw 실행 권한이 없다는 메시지가 뜬다면 다음 명령어로 실행 권한을 추가한 뒤 다시 테스트를 수행하면 됩니다.
```
chmod +x mvnw
``` 

앞에 "JUnit 테스트 코드에 시큐리티 적용하기
"까지 잘 적용했다면 정상적으로 테스트를 통과합니다.   
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%201%20-%20EC2%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20Clone%20%EB%B0%9B%EA%B8%B0/4.PNG)  

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)