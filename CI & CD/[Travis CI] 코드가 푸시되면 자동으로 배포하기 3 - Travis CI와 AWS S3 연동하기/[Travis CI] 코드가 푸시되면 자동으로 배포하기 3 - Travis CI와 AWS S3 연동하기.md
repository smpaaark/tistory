S3란 AWS에서 제공하는 일종의 파일 서버입니다.   
이미지 파일을 비롯한 정적 파일들을 관리하거나 지금 진행하는 것처럼 배포 파일들을 관리하는 등의 기능을 지원합니다.   
보통 이미지 업로드를 구현한다면 이 S3를 이용하여 구현하는 경우가 많습니다.   
S3를 비롯한 AWS 서비스와 Travis CI를 연동하게 되면 전체 구조는 다음과 같습니다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/1.jpg)   

첫 번째 단계로 Travis CI와 S3를 연동하겠습니다.   
실제 배포는 AWS CodeDeploy라는 서비스를 이용합니다.   
하지만, S3 연동이 먼저 필요한 이유는 Jar 파일을 전달하기 위해서입니다.   

CodeDeploy는 저장 기능이 없습니다.   
그래서 Travis CI가 빌드한 결과물을 받아서 CodeDeploy가 가져갈 수 있도록 보관할 수 있는 공간이 필요합니다.   
보통은 이럴 때 AWS S3를 이용합니다.   
> CodeDeploy가 빌드도 하고 배포도 할 수 있습니다.   
> CodeDeploy에서는 깃허브 코드를 가져오는 기능을 지원하기 때문입니다.
> 하지만 이렇게 할 때 빌드 없이 배포만 필요할 때 대응하기 어렵습니다.

> 빌드와 배포가 분리되어 있으면 예전에 빌드되어 만들어진 Jar를 재사용하면 되지만, CodeDeploy가 모든 것을 하게 될 땐 항상 빌드를 하게 되니 확장성이 많이 떨어집니다.   
> 그래서 웬만하면 빌드와 배포는 분리하는 것이 좋습니다.   

## AWS Key 발급
일반적으로 AWS 서비스에 외부 서비스가 접근할 수 없습니다.   
그러므로 접근 가능한 권한을 가진 Key를 생성해서 사용해야 합니다.   
AWS에서는 이러한 인증과 관련된 기능을 제공하는 서비스로 IAM(Identity and Access Management)이 있습니다.   

IAM은 AWS에서 제공하는 서비스의 접근 방식과 권한을 관리합니다.   
이 IAM을 통해 Travis CI가 AWS의 S3와 CodeDeploy에 접근할 수 있도록 해보겠습니다.   
AWS 웹 콘솔에서 IAM을 검색하여 이동합니다.   
IAM 페이지 왼쪽 사이드바에서 [사용자 => 사용자 추가] 버튼을 차례로 클릭합니다.   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/2.PNG)   

![3](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/3.PNG)   

생성할 사용자의 이름과 엑세스 유형을 선택합니다.   
엑세스 유형은 프로그래밍 방식 엑세스입니다.   
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/4.PNG)   

권한 설정 방식은 3개 중 [기존 정책 직접 연결]을 선택합니다.   
![5](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/5.PNG)   

화면 아래 정책 검색 화면에서 s3full로 검색하여 체크하고 다음 권한으로 CodeDeployFull을 검색하여 체크합니다.   
![6](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/6.PNG)   

![7](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/7.PNG)   

실제 서비스 회사에서는 권한도 S3와 CodeDeploy를 분리해서 관리하기도 합니다만, 여기서는 간단하게 둘을 합쳐서 관리하겠습니다.   
2개의 권한이 설정되었으면 다음으로 넘어갑니다.   

태그는 Name 값을 지정하는데, 본인이 인지 가능한 정도의 이름으로 만듭니다.   
![8](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/8.PNG)   

마지막으로 본인이 생서한 권한 설정 항목을 확인합니다.   
![9](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/9.PNG)   

최종 생성 완료되면 다음과 같이 엑세스 키와 비밀 엑세스 키가 생성됩니다.   
이 두 값이 Travis CI에서 사용될 키입니다.   
![10](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/10.PNG)   

## Travis CI에 키 등록
먼저 Travis CI의 설정 화면으로 이동합니다.   
![11](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/11.PNG)   

설정 화면을 아래로 조금 내려보면 Environment Variables 항목이 있습니다.   
여기에 AWS_ACCESS_KEY, AWS_SECRET_KEY를 변수로 해서 IAM 사용자에서 발급받은 키 값들을 등록합니다.   
* AWS_ACCESS_KEY: 엑세스 키 ID
* AWS_SECRET_KEY: 비밀 엑세스 키   

![12](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/12.PNG)   

여기에 등록된 값들을 이제 .travis.yml 에서 $AWS_ACCESS_KEY, $AWS_SECRET_KEY란 이름으로 사용할 수 있습니다.   

## S3 버킷 생성
다음으로 S3(Simple Storage Service)에 관해 설정을 진행하겠습니다.   
AWS의 S3 서비스는 일종의 파일 서버입니다.   
순수하게 파일들을 저장하고 접근 권한을 관리, 검색 등을 지원하는 파일 서버의 역할을 합니다.   

S3는 보통 게시글을 쓸 때 나오는 첨부파일 등록을 구현할 때 많이 이용합니다.   
파일 서버의 역할을 하기 때문인데, Travis CI에서 생성된 Build 파일을 저장하도록 구성하겠습니다.   
S3에 저장된 Build 파일은 이후 AWS의 CodeDeploy에서 배포할 파일로 가져가도록 구성할 예정입니다.   
AWS 서비스에서 S3를 검색하여 이동하고 버킷을 생성합니다.   
![13](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/13.PNG)   

![14](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/14.PNG)   

원하는 버킷명을 작성합니다.   
이 버킷에 배포할 Zip 파일이 모여있는 장소임을 의미하도록 짓는 것을 추천합니다.   
![15](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/15.PNG)

다음으로 넘어가서 버전관리를 설정합니다.   
별다른 설정을 할 것이 없으니 바로 넘어갑니다.   
![16](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/16.PNG)   

다음으로는 버킷의 보안과 권한 설정 부분입니다.   
퍼블릭 액세스를 열어두는 분이 있을 텐데, 모두 차단을 해야 합니다.   
현재 프로젝트야 이미 깃허브에 오픈소스로 풀려있으니 문제없지만, 실제 서비스에서 할 때는 Jar 파일이 퍼블릭일 경우 누구나 내려받을 수 있어 코드나 설정값, 주요 키값들이 다 탈취될 수 있습니다.   

퍼블릭이 아니더라도 우리는 IAM 사용자로 발급받은 키를 사용하니 접근 가능합니다.   
그러므로 모든 액세스를 차단하는 설정에 체크합니다.   
![17](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/17.PNG)   

버킷이 생성되면 다음과 같이 버킷 목록에서 확인할 수 있습니다.   
![18](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/18.PNG)   

## .travis.yml 추가
Travis CI에서 빌드하여 만든 Jar 파일을 S3에 올릴 수 있도록 .travis.yml에 다음 코드를 추가합니다.   
```
...

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

...
```

전체 코드는 다음과 같습니다.   
Travis CI Settings 항목에서 등록한 $AWS_ACCESS_KEY와 $AWS_SECRET_KEY가 변수로 사용됩니다.   
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

# 1
before_deploy:
  # 2
  - zip -r used-car-admin *
  # 3
  - mkdir -p deploy
  # 4
  - mv used-car-admin.zip deploy/used-car-admin.zip

# 5
deploy:
  - provider: s3
    access_key_id: $AWS_ACCESS_KEY # Travis repo settings에 설정된 값
    secret_access_key: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    bucket: used-car-admin-build # S3 버킷
    region: ap-northeast-2
    skip_cleanup: true
    acl: private # zip 파일 접근을 private으로
    # 6
    local_dir: deploy # before_deploy에서 생성한 디렉토리
    wait-until-deployed: true

# CI 실행 완료시 메일로 알람
notifications:
  email:
    recipients:
      - smpaaark@gmail.com
```
1. before_deploy
  * deploy 명령어가 실행되기 전에 실행됩니다.   
  * CodeDeploy는 Jar 파일은 인식하지 못하므로 Jar+기타 설정 파일들을 모아 압축(zip)합니다.   
2. zip -r used-car-admin
  * 현재 위치의 모든 파일을 used-car-admin 이름으로 압축(zip)합니다.   
  * 명령어의 마지막 위치는 본인의 프로젝트 이름이어야 합니다.   
3. mkdir -p deploy
  * deploy라는 디렉토리를 Travis CI가 실행 중인 위치에서 생성합니다.   
4. mv used-car-admin.zip deploy/used-car-admin.zip
  * used-car-admin.zip 파일을 deploy/used-car-admin.zip으로 이동시킵니다.   
5. deploy
  * S3로 파일 업로드 혹은 CodeDeploy로 배포 등 외부 서비스와 연동될 행위들을 선언합니다.   
6. local_dir: deploy
  * 앞에서 생성한 deploy 디렉토리를 지정합니다.
  * 해당 위치의 파일들만 S3로 전송합니다.

설정이 다 되었으면 깃허브로 푸시합니다.   
Travis CI에서 자동으로 빌드가 진행되는 것을 확인하고, 모든 빌드가 성공하는지 확인합니다.   
다음 로그가 나온다면 Travis CI의 빌드가 성공한 것입니다.   
![19](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/19.PNG)   

그리고 S3 버킷을 가보면 업로드가 성공한 것을 확인할 수 있습니다.   
![20](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%203%20-%20Travis%20CI%EC%99%80%20AWS%20S3%20%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0/20.PNG)   

Travis CI를 통해 자동으로 파일이 올려진 것을 확인할 수 있습니다.   

Travis CI와 S3 연동이 완료되었습니다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)
* [[Spring Boot] Chap 9.Travis CI 배포 자동화](https://slo-ow.github.io/spring/spring-freelec-springboot-chap9/)