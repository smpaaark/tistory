아마존 리눅스 서버를 사용할 때 몇 가지 설정들이 필요합니다.   
이 설정들은 모두 자바 기반의 웹 애플리케이션 (톰캣과 스프링부트)가 작동해야 하는 서버들에선 필수로 해야 하는 설정들입니다.   
* Java 8 설치
  * 현재 이 프로젝트의 버전은 Java 8 입니다.
* 타임존 변경
  * 기본 서버의 시간은 미국 시간대입니다.   
  * 한국 시간대가 되어야만 우리가 사용하는 시간이 모두 한국 시간으로 등록되고 사용됩니다.
* 호스트네임 변경
  * 현재 접속한 서버의 별명을 등록합니다.
  * 실무에서는 한 대의 서버가 아닌 수십 대의 서버가 작동되는 데, IP만으로 어떤 서버가 어떤 역할을 하는지 알 수 없습니다.
  * 이를 구분하기 위해 보통 호스트 네임을 필수로 등록합니다.

앞에 진행한 EC2 접속 과정을 통해서 EC2에 접속한 뒤에 다음 과정을 진행하면 됩니다.   

## Java 8 설치
현재 프로젝트의 버전이 Java 8이므로 Java 8을 EC2에 설치하겠습니다.   
EC2에서 다음의 명령어를 실행합니다.   
```
sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```

설치가 완료되었다면 인스턴스의 Java 버전을 8로 변경하겠습니다.
```
sudo /usr/sbin/alternatives --config java
```
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20AWS%20%EC%84%9C%EB%B2%84%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20%EC%95%84%EB%A7%88%EC%A1%B4%20%EB%A6%AC%EB%88%85%EC%8A%A4%20%EC%84%9C%EB%B2%84%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EA%BC%AD%20%ED%95%B4%EC%95%BC%20%ED%95%A0%20%EC%84%A4%EC%A0%95%EB%93%A4/1.PNG)   

현재 Java 8만 설치되어 있으니 따로 작업할 필요가 없습니다.   
현재 버전이 Java8이 되었는지 확인합니다.
```
java -version
```
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20AWS%20%EC%84%9C%EB%B2%84%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20%EC%95%84%EB%A7%88%EC%A1%B4%20%EB%A6%AC%EB%88%85%EC%8A%A4%20%EC%84%9C%EB%B2%84%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EA%BC%AD%20%ED%95%B4%EC%95%BC%20%ED%95%A0%20%EC%84%A4%EC%A0%95%EB%93%A4/2.PNG)   

## 타임존 변경
EC2 서버의 기본 타임존은 UTC입니다.   
이는 세계 표준 시간으로 한국의 시간대가 아닙니다.   
즉, 한국의 시간과는 9시간 차이가 발생합니다.   
이렇게 되면 서버에서 수행되는 Java 애플리케이션에서 생성되는 시간도 모두 9시간씩 차이가 나기 때문에 꼭 수정해야 할 설정입니다.   
서버의 타임존을 한국 시간(KST)로 변경하겠습니다.   

다음 명령어를 차례로 수행합니다.
```
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

정상적으로 수행되었다면 date 명령어로 타임존이 KST로 변경된 것을 확인할 수 있습니다.   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20AWS%20%EC%84%9C%EB%B2%84%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20%EC%95%84%EB%A7%88%EC%A1%B4%20%EB%A6%AC%EB%88%85%EC%8A%A4%20%EC%84%9C%EB%B2%84%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EA%BC%AD%20%ED%95%B4%EC%95%BC%20%ED%95%A0%20%EC%84%A4%EC%A0%95%EB%93%A4/3.PNG)   

## Hostname 변경
여러 서버를 관리 중일 경우 IP만으로 어떤 서비스의 서버인지 확인이 어렵습니다.   
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20AWS%20%EC%84%9C%EB%B2%84%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20%EC%95%84%EB%A7%88%EC%A1%B4%20%EB%A6%AC%EB%88%85%EC%8A%A4%20%EC%84%9C%EB%B2%84%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EA%BC%AD%20%ED%95%B4%EC%95%BC%20%ED%95%A0%20%EC%84%A4%EC%A0%95%EB%93%A4/4.PNG)
> ip-172-31-6-231이 무슨 서비스인지 알 수 없습니다.

그래서 각 서버가 어느 서비스인지 표현하기 위해 HOSTNAME을 변경하겠습니다.  
[Amazon Linux 인스턴스에서 호스트 이름 변경](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/set-hostname.html) 사이트에 상세하게 나와있습니다.   

호스트 이름 업데이트를 유지하려면 preserve_hostname cloud-init 설정이 true로 설정되어 있는지 확인해야 합니다.    
다음 명령을 실행하여 이 설정을 편집하거나 추가할 수 있습니다.
```
sudo vi /etc/cloud/cloud.cfg
```

preserve_hostname 설정이 나열되어 있지 않으면 파일 끝에 다음 텍스트 줄을 추가합니다.   
```
preserve_hostname: true
```

시스템 호스트 이름을 퍼블릭 DNS 이름으로 변경하려면 다음을 수행합니다.   
* Amazon Linux 2
  * hostnamectl 명령으로 호스트 이름을 설정하여 원하는 시스템 호스트 이름을 반영합니다.     
  ```
  sudo hostnamectl set-hostname used-car-admin.localdomain
  ```

* 선호하는 텍스트 편집기로 /etc/hosts 파일을 열고 127.0.0.1로 시작되는 항목을 아래 예제와 일치하도록 변경합니다.    
원하는 호스트 이름을 대신 입력하면 됩니다.   
```
sudo vim /etc/hosts
```
```
127.0.0.1 used-car-admin.localdomain used-car-admin localhost4 localhost4.localdomain4
```

* Amazon EC2 콘솔을 사용하여 재부팅할 수 있습니다(인스턴스 페이지에서 인스턴스를 선택하고 인스턴스 상태, 인스턴스 재부팅을 차례로 선택).
* 인스턴스에 로그인하고 호스트 이름이 업데이트되었는지 확인합니다.    
프롬프트에 새 호스트 이름이 첫 번째 "."까지 표시되어야 하고, hostname 명령이 정규화된 도메인 이름을 표시해야 합니다.      

![7](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20AWS%20%EC%84%9C%EB%B2%84%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20%EC%95%84%EB%A7%88%EC%A1%B4%20%EB%A6%AC%EB%88%85%EC%8A%A4%20%EC%84%9C%EB%B2%84%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EA%BC%AD%20%ED%95%B4%EC%95%BC%20%ED%95%A0%20%EC%84%A4%EC%A0%95%EB%93%A4/7.PNG)

정상적으로 등록되었는지 확인해 봅니다.   
확인 방법은 다음 명령어로 합니다.   
```
curl 등록한 호스트 이름
```

잘 등록하였다면 다음과 같이 80 포트로 접근이 안 된다는 에러가 발생합니다.   
![8](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20AWS%20%EC%84%9C%EB%B2%84%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20%EC%95%84%EB%A7%88%EC%A1%B4%20%EB%A6%AC%EB%88%85%EC%8A%A4%20%EC%84%9C%EB%B2%84%20%EC%83%9D%EC%84%B1%20%EC%8B%9C%20%EA%BC%AD%20%ED%95%B4%EC%95%BC%20%ED%95%A0%20%EC%84%A4%EC%A0%95%EB%93%A4/8.PNG)   

이는 아직 80포트로 실행된 서비스가 없음을 의미합니다.   
즉, curl 호스트 이름으로 실행은 잘 되었음을 의미합니다.   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)