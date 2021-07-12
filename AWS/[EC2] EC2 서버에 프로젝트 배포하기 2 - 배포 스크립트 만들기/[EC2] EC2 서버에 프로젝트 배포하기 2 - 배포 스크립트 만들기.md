작성한 코드를 실제 서버에 반영하는 것을 배포라고 합니다.   
배포라 하면 다음의 과정을 모두 포괄하는 의미라고 보면 됩니다.   
* git clone 혹은 git pull을 통해 새 버전의 프로젝트 받음
* Gradle이나 Maven을 통해 프로젝트 테스트와 빌드
* EC2 서버에서 해당 프로젝트 실행 및 재실행

앞선 과정을 배포할 때마다 개발자가 하나하나 명령어를 실행하는 것은 불편함이 많습니다.   
그래서 이를 쉘 스크립트로 작성해 스크립트만 실행하면 앞의 과정이 차례로 진행되도록 하겠습니다.   
참고로 쉘 스크립트와 빔(vim)은 서로 다른 역할을 합니다.   
쉘 스크립트는 .sh라는 파일 확장자를 가진 파일입니다.   
노드 JS가 .js라는 파일을 통해 서버에서 작동하는 것처럼 쉘 스크립트 역시 리눅스에서 기본적으로 사용할 수 있는 스크립트 파일의 한 종류입니다.   

빔은 리눅스 환경과 같이 GUI가 아닌 환경에서 사용할 수 있는 편집 도구 입니다.   

리눅스에서는 빔 외에도 이맥스(Emacs), 나도(NANO) 등의 도구를 지원합니다만, 가장 대중적인 도구가 빔이다보니 이 책에서도 역시 빔으로 리눅스 환경에서의 편집을 진행하겠습니다.   

~/app/step1/에 deploy.sh 파일을 하나 생성합니다.    
```
vim ~/app/step1/deploy.sh
```

다음의 코드를 추가합니다.   
```
#!/bin/bash

# 1
REPOSITORY=/home/ec2-user/app/step1
PROJECT_NAME=used-car-admin

# 2
cd $REPOSITORY/$PROJECT_NAME/

# 3
echo "> Git Pull"

git pull origin master

echo "> 프로젝트 Build 시작"

# 4
./mvnw package

echo "> step1 디렉토리로 이동"

cd $REPOSITORY

echo "> Build 파일 복사"

# 5
cp $REPOSITORY/$PROJECT_NAME/target/*.jar $REPOSITORY/

echo "> 현재 구동중인 애플리케이션 pid 확인"

# 6
CURRENT_PID=$(pgrep -f ${PROJECT_NAME}.*.jar)

echo "현재 구동 중인 애플리케이션 pid: $CURRENT_PID"

# 7
if [ -z "$CURRENT_PID" ]; then
    echo "> 현재 구동 중인 애플리케이션이 없으므로 종료하지 않습니다."
else
    echo "> kill -15 $CURRENT_PID"
    kill -15 $CURRENT_PID
    sleep 5
fi

echo "> 새 애플리케이션 배포"

# 8
JAR_NAME=$(ls -tr $REPOSITORY/ | grep jar | tail -n 1)

echo "> JAR Name: $JAR_NAME"

# 9
nohup java -jar $REPOSITORY/$JAR_NAME 2>&1 &
```
1. REPOSITORY=/home/ec2-user/app/step1
* 프로젝트 디렉토리 주소는 스크립트 내에서 자주 사용되는 값이기 때문에 이를 변수로 저장합니다.
* 마찬가지로 PROJECT_NAME=used-car-admin도 동일하게 변수로 저장합니다.
* 쉘에서는 타입 없이 선언하여 저장합니다.
* 쉘에서는 $변수명으로 변수를 사용할 수 있습니다.

2. cd $REPOSITORY/$PROJECT_NAME/
* 제일 처음 git clone 받았던 디렉토리로 이동합니다.
* 바로 위의 쉘 변수 설명을 따라 /home/ec2-user/app/step1/used-car-admin 주소로 이동합니다.

3. git pull origin master
* 디렉토리 이동 후, master 브랜치의 최신 내용을 받습니다.

4. ./mvnw package
* 프로젝트 내부의 mvnw로 package를 수행합니다.

5. cp ./target/*.jar $REPOSITORY/
* build의 결과물인 jar 파일을 복사해 jar 파일을 모아둔 위치로 복사합니다.

6. CURRENT_PID=$(pgrep -f ${PROJECT_NAME}.*.jar)
* 기존에 수행 중이던 스프링 부트 애플리케이션을 종료합니다.
* pgrep은 process id만 추출하는 명령어입니다.
* -f 옵션은 프로세스 이름으로 찾습니다.

7. if ~ else ~ fi
* 현재 구동 중인 프로세스가 있는지 없는지를 판단해서 기능을 수행합니다.
* process id 값을 보고 프로세스가 있으면 해당 프로세스를 종료합니다.

8. JAR_NAME=$(ls -tr $REPOSITORY/ | grep jar | tail -n 1)
* 새로 실행할 jar 파일명을 찾습니다.
* 여러 jar 파일이 생기기 때문에 tail -n으로 가장 나중의 jar 파일(최신파일)을 변수에 저장합니다.

9. nohup java -jar $REPOSITORY/$JAR_NAME 2>&1 &
* 찾은 jar 파일명으로 해당 jar 파일을 nohup으로 실행합니다.
* 스프링 부트의 장점으로 특별히 외장 톰캣을 설치할 필요가 없습니다.
* 내장 톰캣을 사용해서 jar 파일만 있으면 바로 웹 애플리케이션 서버를 실행할 수 있습니다.
* 일반적으로 자바를 실행할 때는 java -jar라는 명령어를 사용하지만, 이렇게 하면 사용자가 터미널 접속을 끊을 때 애플리케이션도 같이 종료됩니다.
* 애플리케이션 실행자가 터미널을 종료해도 애플리케이션은 계속 구동될 수 있도록 nohup 명령어를 사용합니다.

이렇게 생성한 스크립트에 실행 권한을 추가합니다.
```
chmod +x ./deploy.sh
```

그리고 다시 확인해 보면 x 권한이 추가된 것을 확인할 수 있습니다.   
![1]()   

이제 이 스크립트를 다음 명령어로 실행합니다.
```
./deploy.sh
```

그럼 다음과 같이 로그가 출력되며 애플리케이션이 실행됩니다.   
![2]()   





## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)