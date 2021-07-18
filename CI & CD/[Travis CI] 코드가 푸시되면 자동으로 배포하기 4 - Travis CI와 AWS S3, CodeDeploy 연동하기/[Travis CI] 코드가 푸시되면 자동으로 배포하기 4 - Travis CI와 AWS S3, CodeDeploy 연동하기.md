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
CodeDeploy에서 EC2에 접근하려면 마찬가지로 권한이 필요합니다.   
AWS의 서비스이니 IAM 역할을 생성합니다.   
서비스는 [AWS 서비스 => CodeDeploy]를 차례로 선택합니다.   
![11]()   

CodeDeploy는 권한이 하나뿐이라서 선택 없이 바로 다음으로 넘어가면 됩니다.   
![12]()   

태그 역시 본인이 원하는 이름으로 짓습니다.   
![13]()   

CodeDeploy를 위한 역할 이름과 선택 항목들을 확인한 뒤 생성 완료를 합니다.   
![14]()   

## CodeDeploy 생성
CodeDeploy는 AWS의 배포 삼형제 중 하나입니다.   
배포 삼형제에 대해 간단하게 소개하자면 다음과 같습니다.   
* Code Commit
  * 깃허브와 같은 코드 저장소의 역할을 합니다.   
  * 프라이빗 기능을 지원한다는 강점이 있지만, 현재 깃허브에서 무료로 프라이빗 지원을 하고 있어서 거의 사용되지 않습니다.   
* Code Build
  * Travis CI와 마찬가지로 빌드용 서비스입니다.
  * 멀티 모듈을 배포해야 하는 경우 사용해 볼만하지만, 규모가 있는 서비스에서는 대부분 젠킨스/팀시티 등을 이용하니 이것 역시 사용할 일이 거의 없습니다.   
* CodeDeploy
  * AWS의 배포 서비스입니다.   
  * 앞에서 언급한 다른 서비스들은 대체재가 있고, 딱히 대체재보다 나은 점이 없지만, CodeDeploy는 대체재가 없습니다.   
  * 오토 스케일링 그룹 배포, 블루 그린 배포, 롤링 배포, EC2 단독 배포 등 많은 기능을 지원합니다.   

이 중에서 현재 진행 중인 프로젝트에서는 Code Commit의 역할은 깃허브가, Code Build의 역할은 Travis CI가 하고 있습니다.   
그래서 우리가 추가로 사용할 서비스는 CodeDeploy입니다.   

CodeDeploy 서비스로 이동해서 화면 중앙에 있는 [애플리케이션 생성] 버튼을 클릭합니다.   
![15]()   

생성할 CodeDeploy의 이름과 컴퓨팅 플랫폼을 선택합니다.   
컴퓨팅 플랫폼에선 [EC2/온프레미스]를 선택하면 됩니다.   
![16]()   

생성이 완료되면 배포 그룹을 생성하라는 메시지를 볼 수 있습니다.   
화면 중앙의 [배포 그룹 생성] 버튼을 클릭합니다.   
![17]()   

배포 그룹 이름과 서비스 역할을 등록합니다.   
서비스 역할은 좀 전에 생성한 CodeDeploy용 IAM 역할을 선택하면 됩니다.   
![18]()   

배포 유형에서는 현재 위치를 선택합니다.   
만약 본인이 배포할 서비스가 2대 이상이라면 블루/그린을 선택하면 됩니다.   
여기선 1대의 EC2에만 배포하므로 선택하지 않습니다.   
![19]()   

환경 구성에서는 [Amazon EC2 인스턴스]에 체크합니다.   
![20]()   

마지막으로 다음과 같이 배포 구성을 선택하고 로드밸런싱 체크 해제합니다.   
![21]()   

배포 구성이란 한번 배포할 때 몇 대의 서버에 배포할지를 결정합니다.   
2대 이상이라면 1대씩 배포할지, 30% 혹은 50%로 나눠서 배포할지 등등 여러 옵션을 선택하겠지만, 1대 서버다 보니 전체 배포하는 옵션으로 선택하면 됩니다.   

배포 그룹까지 생성되셨다면 CodeDeploy 설정은 끝입니다.   

## Travis CI, S3, CodeDeploy 연동
먼저 S3에서 넘겨줄 zip 파일을 저장할 디렉토리를 하나 생성하겠습니다.   
EC2 서버에 접속해서 다음과 같이 디렉토리를 생성합니다.   
```
mkdir ~/app/step2 && mkdir ~/app/step2/zip
```

Travis CI의 Build가 끝나면 S3에 zip 파일이 전송되고, 이 zip 파일은 /home/ec2-user/app/step2/zip로 복사되어 압축을 풀 예정입니다.   

Travis CI의 설정은 .travis.yml로 진행했습니다.   

AWS CodeDeploy의 설정은 appspec.yml로 진행합니다.   
![22]()   

코드는 다음과 같습니다.   
```
# 1
version: 0.0
os: linux
files:
  # 2
  - source:  /
    # 3
    destination: /home/ec2-user/app/step2/zip/
    # 4
    overwrite: yes
```
1. version: 0.0
  * CodeDeploy 버전을 이야기합니다.
  * 프로젝트 버전이 아니므로 0.0 외에 다른 버전을 사용하면 오류가 발생합니다.
2. source
  * CodeDeploy에서 전달해 준 파일 중 destination으로 이동시킬 대상을 지정합니다.   
  * 루트 경로(/)를 지정하면 전체 파일을 이야기합니다.
3. destination
  * source에서 지정된 파일을 받을 위치입니다.
  * 이후 Jar를 실행하는 등은 destination에서 옮긴 파일들로 진행됩니다.
4. overwrite
  * 기존에 파일들이 있으면 덮어쓸지를 결정합니다.
  * 현재 yes라고 했으니 파링들을 덮어쓰게 됩니다.

.trvis.yml에도 CodeDeploy 내용을 추가합니다.   
deploy 항목에 다음 코드를 추가합니다.   
```
deploy:
  ...

  - provider: codedeploy
    access_key_id: $AWS_ACCESS_KEY # Travis repo settings에 설정된 값
    secret_access_key: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    bucket: used-car-admin-build # S3 버킷
    key: used-car-admin.zip # 빌드 파일을 압축해서 전달
    bundle_type: zip
    application: used-car-admin # 웹 콘솔에서 등록한 CodeDeploy 어플리케이션
    deployment_group: used-car-admin-group # 웹 콘솔에서 등록한 CodeDeploy 배포 그룹
    region: ap-northeast-2
    wait-until-deployed: true
```

S3 옵션과 유사합니다.   
다른 부분은 CodeDeploy의 애플리케이션 이름과 배포 그룹명을 지정하는 것입니다.   

전체 코드는 다음과 같습니다.   
```
language: java
jdk:
  - openjdk8

branches:
  only:
    - master

# Travis CI 서버의 Home
cache:
  directories:
    - '$HOME/.m2'

before_install:
  - chmod +x mvnw

script: "./mvnw clean package"

before_deploy:
  - zip -r used-car-admin *
  - mkdir -p deploy
  - mv used-car-admin.zip deploy/used-car-admin.zip

deploy:
  - provider: s3
    access_key_id: $AWS_ACCESS_KEY # Travis repo settings에 설정된 값
    secret_access_key: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    bucket: used-car-admin-build # S3 버킷
    region: ap-northeast-2
    skip_cleanup: true
    acl: private # zip 파일 접근을 private으로
    local_dir: deploy # before_deploy에서 생성한 디렉토리
    wait-until-deployed: true

  - provider: codedeploy
    access_key_id: $AWS_ACCESS_KEY # Travis repo settings에 설정된 값
    secret_access_key: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    bucket: used-car-admin-build # S3 버킷
    key: used-car-admin.zip # 빌드 파일을 압축해서 전달
    bundle_type: zip
    application: used-car-admin # 웹 콘솔에서 등록한 CodeDeploy 어플리케이션
    deployment_group: used-car-admin-group # 웹 콘솔에서 등록한 CodeDeploy 배포 그룹
    region: ap-northeast-2
    wait-until-deployed: true

# CI 실행 완료시 메일로 알람
notifications:
  email:
    recipients:
      - smpaaark@gmail.com
```

모든 내용을 작성했다면 프로젝트를 커밋하고 푸시합니다.   
깃허브로 푸시가 되면 Travis CI가 자동으로 시작됩니다.   

Travis CI가 끝나면 CodeDeploy 화면 아래에서 배포가 수행되는 것을 확인할 수 있습니다.   
![23]()   

배포가 끝났다면 다음 명령어로 파일들이 잘 도착했는지 확인해 봅니다.   
```
cd /home/ec2-user/app/step2/zip
```

그럼 다음과 같이 프로젝트 파일들이 잘 도착한 것을 확인할 수 있습니다.   
![24]()   

Travis CI와 S3, CodeDeploy가 연동이 완료되었습니다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)