로컬 PC에서 RDS로 접근하기 위해서 RDS의 보안 그룹에 본인 PC의 IP를 추가하겠습니다.   
RDS의 세부정보 페이지에서 [보안 그룹] 항목을 클릭합니다.   
![1]()   
> rds-launch-wizard라는 새로운 보안 그룹을 생성했습니다.

RDS의 보안 그룹 정보를 그대로 두고, 브라우저를 새로 열어 봅니다.   
그래서 브라우저 다른 창에서는 보안 그룹 목록 중 EC2에 사용된 보안 그룹의 그룹 ID를 복사합니다.   
![2]()   

복사된 보안 그룹 ID와 보인의 IP를 RDS 보안 그룹의 인바운드로 추가합니다.   
![3]()   

인바운드 규칙 유형에서는 MYSQL/Aurora를 선택하시면 자동으로 3306포트가 선택됩니다.   
* 보안 그룹 첫 번째 줄: 현재 내 PC의 IP를 등록합니다.
* 보안 그룹 두 번째 줄: EC2의 보안 그룹을 추가합니다.
  * 이렇게 하면 EC2와 RDS 간에 접근이 가능합니다.   
  * EC2의 경우 이후에 2대 3대가 될 수도 있는데, 매번 IP를 등록할 수는 없으니 보편적으로 이렇게 보안 그룹 간에 연동을 진행합니다.   

## Workbench 접속
본인이 생성한 RDS의 정보를 차례로 등록합니다.   
![9]()   

마스터 계정명과 비밀번호를 등록한 뒤, 화면 아래의 [Test Connection]을 클릭해 연결 테스트를 해봅니다.   
![10]()   

Successful 메시지를 보았다면 [Ok] 버튼을 눌러 최종 저장을 합니다.   

콘솔창에서 SQL을 실행해 보겠습니다.   
쿼리가 수행될 database를 선택하는 쿼리입니다.
```
use {AWS RDS 웹콘솔에서 지정한 데이터베이스명};
```

만약 본인이 RDS 생성 시 지정한 Database 명을 잊었다면 왼쪽의 schema 항목을 보면 MySQL에서 기본으로 생성하는 싀마 외에 다른 스키마가 1개 추가되어 있으니 이를 확인하면 됩니다.   
![11]()   

쿼리는 다음과 같이 실행합니다.   
쿼리문을 드래그로 선택한 뒤 [Ctrl + Shift + Enter]를 하면 됩니다.   
```
use used_car_admin;
```

다음 그림과 같이 화면 아래에 성공 표시가 떴다면 쿼리가 정상적으로 수행된 것입니다.   
![12]()   

데이터베이스가 선택된 상태에서 현재의 character_set, collation 설정을 확인합니다.   
```
show variables like 'c%';
```

쿼리 결과를 보면 다른 필드들은 모두 잘 적용되었는데 character_set_database, collation_database가 latin1로 되어있습니다.   

MariaDB에서만 RDS 파라미터 그룹으로는 변경이 안되는 필드들이 있습니다.   
그래서 직접 변경하겠습니다.   
다음 쿼리를 실행합니다.
```
ALTER DATABASE 데이터베이스명
CHARACTER SET = 'utf8mb4'
COLLATE = 'utf8mb4_general_ci';
```

쿼리를 수행하였다면 다시 한번 character set을 확인해 봅니다.   
![13]()   

성공적으로 모든 항목이 utf8mb4로 변경된 것을 확인하였습니다.   
빠르게 타임존까지 아래 쿼리로 확인해 봅니다.   
```
select @@time_zone, now();
```
![14]()   

RDS 파라미터 그룹이 잘 적용되어 한국 시간으로 된 것을 확인하였습니다.   

마지막으로 한글명이 잘 들어가는지 간단히 테이블 생성과 insert 쿼리를 실행해 봅니다.   
> 테이블 생성은 인코딩 설정 변경 전에 생성되면 안됩니다.   
> 만들어질 당시의 설정값을 그대로 유지하고 있어, 자동 변경이 되지 않고 강제로 변경해야 합니다.   
> 웬만하면 테이블은 모든 설정이 끝난 후에 생성하시는 것이 좋습니다.   

다음 쿼리를 차례로 실행 해봅니다.   
```
CREATE TABLE test (
    id bigint(20) NOT NULL AUTO_INCREMENT,
    content varchar(255) DEFAULT NULL,
    PRIMARY KEY (id)
) ENGINE=InnoDB;

insert into test(content) values ('테스트');

select * from test;
```

다음과 같이 한글 데이터도 잘 등록되는 것이 확인됩니다.   
![15]()   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)