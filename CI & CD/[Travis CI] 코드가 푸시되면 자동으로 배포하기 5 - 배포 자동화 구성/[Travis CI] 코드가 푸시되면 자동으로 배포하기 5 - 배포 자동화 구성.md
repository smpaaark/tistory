이제 실제로 Jar를 배포하여 실행까지 해보겠습니다.   

## deploy.sh 파일 추가
먼저 step2 환경에서 실행될 deploy.sh를 생성하겠습니다.   
scripts 디렉토리를 생성하여 여기에 스크립트를 생성합니다.   
```
#!/bin/bash

REPOSITORY=/home/ec2-user/app/step2
PROJECT_NAME=used-car-admin

echo "> Build 파일 복사"

cp $REPOSITORY/zip/*.jar $REPOSITORY/

echo "> 현재 구동중인 애플리케이션 pid 확인"

# 1
CURRENT_PID=$(pgrep -fl used-car-admin | grep jar | awk '{print $1}')

echo "현재 구동중인 어플리케이션 pid: $CURRENT_PID"

if [ -z "$CURRENT_PID" ]; then
    echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다."
else
    echo "> kill -15 $CURRENT_PID"
    kill -15 $CURRENT_PID
    sleep 5
fi

echo "> 새 어플리케이션 배포"

JAR_NAME=$(ls -tr $REPOSITORY/*.jar | tail -n 1)

echo "> JAR Name: $JAR_NAME"

echo "> $JAR_NAME 에 실행권한 추가"

# 2
chmod +x $JAR_NAME

echo "> $JAR_NAME 실행"

nohup java -jar \
    -Dspring.config.location=classpath:/application.properties,classpath:/application-real.properties,/home/ec2-user/app/application-oauth.properties,/home/ec2-user/app/application-real-db.properties \
    -Dspring.profiles.active=real \
    # 3
    $JAR_NAME > $REPOSITORY/nohup.out 2>&1 &
```
1. CURRENT_PID
  * 현재 수행 중인 스프링 부트 애플리케이션의 프로세스 ID를 찾습니다.
  * 실행 중이면 종료하기 위해서입니다.
  * 스프링 부트 애플리케이션 이름(used-car-admin)으로 된 다른 프로그램들이 있을 수 있어 used-car-admin으로 된 jar(pgrep -fl used-car-admin | grep.jar) 프로세스를 찾은 뒤 ID를 찾습니다(| awk '{print $1}').
2. chmod +x $JAR_NAME
  * Jar 파일은 실행 권한이 없는 상태입니다.
  * nohup으로 실행할 수 있게 실행 권한을 부여합니다.
3. $JAR_NAME > $REPOSITORY/nohup.out 2>&1 &
  * nohup 실행 시 CodeDeploy는 무한 대기합니다.
  * 이 이슈를 해결하기 위해 nohup.out 파일을 표준 입출력용으로 별도로 사용합니다.
  * 이렇게 하지 않으면 nohup.out 파일이 생기지 않고, CodeDeploy 로그에 표준 입출력이 출력됩니다.
  * nohup이 끝나기 전까지 CodeDeploy도 끝나지 않으니 꼭 이렇게 해야만 합니다.

step1에서 작성된 deploy.sh와 크게 다르지 않습니다.   
우선 git pull을 통해 직접 빌드했던 부분을 제거했습니다.   
그리고 Jar를 실행하는 단계에서 몇가지 코드가 추가되었습니다.   
> 플러그인 중 BashSupport를 설치하면 .sh 파일 편집 시 도움을 받을 수 있습니다.

## .travis.yml 파일 수정
현재는 프로젝트의 모든 파일을 zip 파일로 만드는데, 실제로 필요한 파일들은 Jar, appspec.yml, 배포를 위한 스크립트들입니다.   
이 외 나머지는 배포에 필요하지 않으니 포함하지 않겠습니다.   
그래서 .travis.yml 파일의 before_deploy를 수정합니다.   
```
before_deploy:
  # 1
  - mkdir -p before-deploy # zip에 포함시킬 파일들을 담을 디렉토리 생성
  # 2
  - cp scripts/*.sh before-deploy/
  - cp appspec.yml before-deploy/
  - cp target/*.jar before-deploy/
  # 3
  - cd before-deploy && zip -r before-deploy * # before-deploy로 이동후 전체 압축
  - cd ../ && mkdir -p deploy # 상위 디렉토리로 이동후 deploy 디렉토리 생성
  - mv before-deploy/before-deploy.zip deploy/used-car-admin.zip # deploy로 zip파일 이동
```
1. Travis CI는 S3로 특정 파일만 업로드가 안됩니다.
  * 디렉토리 단위로만 업로드 할 수 있기 때문에 before-deploy 디렉토리는 항상 생성합니다.
2. before-deploy에는 zip 파일에 포함시킬 파일들을 저장합니다.
3. zip -r 명령어를 통해 before-deploy 디렉토리 전체 파일을 압축합니다.

이 외 나머지 코드는 수정할 것이 없습니다.   

## appspec.yml
appspec.yml 파일에 다음 코드를 추가합니다.   
location, timeout, runas의 들여쓰기를 주의해야 합니다.   
들여쓰기가 잘못될 경우 배포가 실패합니다.   
```
# 1
permissions:
  - object: /
    pattern: "**"
    owner: ec2-user
    group: ec2-user

# 2
hooks:
  ApplicationStart:
    - location: deploy.sh
      timeout: 60
      runas: ec2-user
```
1. permissions
  * CodeDeploy에서 EC2 서버로 넘겨준 파일들을 모두 ec2-user 권한을 갖도록 합니다.   
2. hooks
  * CodeDeploy 배포 단계에서 실행할 명령어를 지정합니다.
  * ApplicationStart라는 단계에서 deploy.sh를 ec2-user 권한으로 실행하게 합니다.
  * timeout: 60으로 스크립트 실행 60초 이상 수행되면 실패가 됩니다(무한정 기다릴 수 없으니 시간제한을 둬야만 합니다)

그래서 전체 코드는 다음과 같습니다.
```
version: 0.0
os: linux
files:
  - source:  /
    destination: /home/ec2-user/app/step2/zip/
    overwrite: yes

permissions:
  - object: /
    pattern: "**"
    owner: ec2-user
    group: ec2-user

hooks:
  ApplicationStart:
    - location: deploy.sh
      timeout: 60
      runas: ec2-user
```

모든 설정이 완료되었으니 깃허브로 커밋과 푸시를 합니다.   
Travis CI에서 다음과 같이 성공 메시지를 확인하고 CodeDeploy에서도 배포가 성공한 것을 확인합니다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20%EB%B0%B0%ED%8F%AC%20%EC%9E%90%EB%8F%99%ED%99%94%20%EA%B5%AC%EC%84%B1/1.PNG)   

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20%EB%B0%B0%ED%8F%AC%20%EC%9E%90%EB%8F%99%ED%99%94%20%EA%B5%AC%EC%84%B1/2.PNG)   

웹 브라우저에서 EC2 도메인을 입력해서 확인해 봅니다.   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20%EB%B0%B0%ED%8F%AC%20%EC%9E%90%EB%8F%99%ED%99%94%20%EA%B5%AC%EC%84%B1/3.PNG)   

### 실제 배포 과정 체험
간단하게나마 변경된 내용을 알 수 있게 src/main/resources/templates/index.mustache 내용에 다음과 같이 ver.2 텍스트를 추가합니다.   
```
...
<h1>🚘중고차 관리 프로그램 ver.2🚖</h1>
...
```

그리고 깃허브로 커밋과 푸시를 합니다.   
그럼 다음과 같이 변경된 코드가 배포된 것을 확인할 수 있습니다.   
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/CI%20%26%20CD/%5BTravis%20CI%5D%20%EC%BD%94%EB%93%9C%EA%B0%80%20%ED%91%B8%EC%8B%9C%EB%90%98%EB%A9%B4%20%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%205%20-%20%EB%B0%B0%ED%8F%AC%20%EC%9E%90%EB%8F%99%ED%99%94%20%EA%B5%AC%EC%84%B1/4.PNG)

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)