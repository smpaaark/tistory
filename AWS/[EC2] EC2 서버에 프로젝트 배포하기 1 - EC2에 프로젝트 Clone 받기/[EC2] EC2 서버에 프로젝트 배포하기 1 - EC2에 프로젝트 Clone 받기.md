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

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)