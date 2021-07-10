EC2에 ssh 접속을 진행합니다.   

접속이되었다면 MySQL 접근 테스트를 위해 MySQL CLI를 설치하겠습니다.   
```
sudo yum install mysql
```
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20EC2%EC%97%90%EC%84%9C%20RDS%EC%97%90%20%EC%A0%91%EA%B7%BC%20%ED%99%95%EC%9D%B8/1.PNG)   

설치가 다 되었으면 로컬에서 접근하듯이 계정, 비밀번호, 호스트 주소를 사용해 RDS에 접속합니다.   
```
mysql -u 계정 -p -h Host 주소
```
```
mysql -u smpaaark -p -h used-car-admin.czgfbzr76ojo.ap-northeast-2.rds.amazonaws.com
```

패스워드를 입력하라는 메시지가 나오면 패스워드까지 입력합니다.   
다음과 같이 EC2에서 RDS로 접속되는 것을 확인할 수 있습니다.   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20EC2%EC%97%90%EC%84%9C%20RDS%EC%97%90%20%EC%A0%91%EA%B7%BC%20%ED%99%95%EC%9D%B8/2.PNG)   

RDS에 접속되었으면 실제로 생성한 RDS가 맞는지 간단한 쿼리를 한번 실행해보겠습니다.
```
show databases;
```
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%204%20-%20EC2%EC%97%90%EC%84%9C%20RDS%EC%97%90%20%EC%A0%91%EA%B7%BC%20%ED%99%95%EC%9D%B8/3.PNG)   

생성했던 used_car_admin이라는 데이터베이스가 있음을 확인했습니다.   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)