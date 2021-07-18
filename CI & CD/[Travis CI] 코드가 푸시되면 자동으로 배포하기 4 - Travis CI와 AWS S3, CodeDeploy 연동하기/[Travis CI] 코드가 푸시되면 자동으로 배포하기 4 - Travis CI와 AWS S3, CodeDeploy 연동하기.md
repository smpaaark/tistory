AWS의 배포 시스템인 CodeDeploy를 이용하기 전에 배포 대상인 EC2가 CodeDeploy를 연동 받을 수 있게 IAM 역할을 하나 생성하겠습니다.   

## EC2에 IAM 역할 추가하기
S3와 마찬가지로 IAM을 검색하고, 이번에는 [역할] 탭을 클릭해서 이동합니다.   
[역할 => 역할 만들기] 버튼을 차례로 클릭합니다.   
![1]()   

앞에서 만들었던 IAM의 사용자와 역할의 차이
* 역할
  * AWS 서비스에서만 할당할 수 있는 권한
  * EC2, CodeDeploy, SQS 등
* 사용자
  * AWS 서비스 외에 사용할 수 있는 권한
  * 로컬 PC, IDC 서버 등

지금 만들 권한은 EC2에서 사용할 것이기 때문에 사용자가 아닌 역할로 처리합니다.   
서비스 선택에서는 [AWS 서비스 => EC2]를 차례로 선택합니다.   
![2]()   

정책에선 EC2RoleForA를 검색하여 AmazonEC2RoleforAWS-CodeDeploy를 선택합니다.   
![3]()   

태그는 본인이 원하는 이름으로 짓습니다.   
![4]()   

마지막으로 역할의 이름을 등록하고 나머지 등록 정보를 최종적으로 확인합니다.   
![5]()   

이렇게 만든 역할을 EC2 서비스에 등록하겠습니다.   
EC2의 인스턴스 목록으로 이동한 뒤, 본인의 인스턴스를 마우스 오른쪽 버튼으로 눌러 [보안 => IAM 역할 수정]를 차례로 선택합니다.   
![6]()   

방금 생성한 역할을 선택합니다.   
![7]()   

역할 선택이 완료되면 해당 EC2 인스턴스를 재부팅 합니다.   
재부팅을 해야만 역할이 정상적으로 적용되니 꼭 한 번은 재부팅해 주세요.   
![8]()   

## CodeDeploy 에이전트 설치
EC2에 접속해서 다음 명령어를 입력합니다.   
```
aws s3 cp s3://aws-codedeploy-ap-northeast-2/latest/install . --region ap-northeast-2
```

내려받기가 성공했다면 다음과 같은 메시지가 콘솔에 출력됩니다.   
![9]()   

install 파일에 실행 권한이 없으니 실행 권한을 추가합니다.   
```
chmod +x ./install
```

install 파일로 설치를 진행합니다.   
```
sudo ./install auto
```

> 만약 설치 중에 다음과 같은 에러가 발생했다면 루비라는 언어가 설치 안 된 상태라서 그렇습니다.   
> /usr/bin/env: ruby: No such file or directory
> 이럴 경우 yum install로 루비를 설치하면 됩니다.   
> sudo yum install ruby

설치가 끝났으면 Agent가 정상적으로 실행되고 있는지 상태 검사를 합니다.   
```
sudo service codedeploy-agent status
```

다음과 같이 running 메시지가 출력되면 정상입니다.   
![10]()   

## CodeDeploy를 위한 권한 생성


## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)