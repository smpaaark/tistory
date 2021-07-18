Travis CI는 깃허브에서 제공하는 무료 CI 서비스입니다.   
> 젠킨스와 같은 CI 도구도 있지만, 젠킨스는 설치형이기 때문에 이를 위한 EC2 인스턴스가 하나 더 필요합니다.
> AWS에서 Travis CI와 같이 CI 도구로 CodeBuild를 제공합니다.   
> 하지만 빌드 시간만큼 요금이 부과되는 구조라 초기에 사용하기에는 부담스럽습니다.   
> 실제 서비스되는 EC2, RDS, S3 외에는 비용 부분을 최소화하는 것이 좋습니다.   

## Travis CI 웹 서비스 설정
https://travis-ci.com/ 에서 깃허브 계정으로 로그인을 한 뒤, github 연동을 활성화 합니다.   
![1]()   

활성화한 저장소를 클릭하면 다음과 같이 저장소 빌드 히스토리 페이지로 이동합니다.   
![2]()   

### 프로젝트 설정
Travis CI의 상세한 설정은 프로젝트에 존재하는 .travis.yml 파일로 할 수 있습니다.   
yml 파일 확장자를 YAML(야믈)이라고 합니다.   

YAML은 쉽게 말해서 JSON에서 괄호를 제거한 것입니다.   
YAML 이념이 "기계에서 파싱하기 쉽게, 사람이 다루기 쉽게"이다 보니 익숙하지 않은 사람이라도 읽고 쓰기가 쉽습니다.   
그러다 보니 현재 많은 프로젝트와 서비스들이 이 YAML을 적극적으로 사용 중입니다.   
Travis CI 역시 설정을 이 YAML을 통해서 하고 있습니다.   

프로젝트의 pom.xml과 같은 위치에서 .travis.yml을 생성한 후 다음의 코드를 추가합니다.   
```
language: java
jdk:
  - openjdk8

# 1
branches:
  only:
    - master

# Travis CI 서버의 Home
# 2
cache:
  directories:
    - '$HOME/.m2'

before_install:
  - chmod +x mvnw

# 3
script: "./mvnw clean package"

# CI 실행 완료시 메일로 알람
# 4
notifications:
  email:
    recipients:
      - smpaaark@gmail.com
```
1. branches
  * Travis CI를 어느 브랜치가 푸시될 때 수행할지 지정합니다.
  * 현재 옵션은 오직 master 브랜치에 push될 때만 수행합니다.
2. cache
  * 메이븐을 통해 의존성을 받게 되면 이를 해당 디렉토리에 캐시하여, 같은 의존성은 다음 배포 때부터 다시 받지 않도록 설정합니다.
3. script
  * master 브랜치에 푸시되었을 때 수행하는 명령어입니다.
  * 여기서는 프로젝트 내부에 둔 mvnw를 통해 clean & package를 수행합니다.
4. notifications
  * Travis CI 실행 완료 시 자동으로 알람이 가도록 설정합니다.

여기까지 마친 뒤, master 브랜치에 커밋과 푸시를 하고, 좀 전의 Travis CI 저장소 페이지를 확인합니다.   
![3]()   

빌드가 성공한 것이 확인되면 .travis.yml에 등록한 이메일을 확인합니다.   
![4]()   

빌드가 성공했다는 것을 메일로도 잘 전달받아 확인했습니다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)