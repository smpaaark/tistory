RDS는 MariaDB를 사용 중입니다.   
이 MariaDB에서 스프링부트 프로젝트를 실행하기 위해선 몇 가지 작업이 필요합니다.   
진행할 작업은 다음과 같습니다.   
* 테이블 생성
  * H2에서 자동 생성해주던 테이블들을 MariaDB에선 직접 쿼리를 이용해 생성합니다.
* 프로젝트 설정
  * 자바 프로젝트가 MariaDB에 접근하려면 데이터베이스 드라이버가 필요합니다.   
  * MariaDB에서 사용 가능한 드라이버를 프로젝트에 추가합니다.
* EC2 (리눅스 서버) 설정
  * 데이터베이스의 접속 정보는 중요하게 보호해야 할 정보입니다.   
  * 공개되면 외부에서 데이터를 모두 가져갈 수 있기 때문입니다.
  * 프로젝트 안에 접속 정보를 갖고 있다면 깃허브와 같이 오픈된 공간에선 누구나 해킹할 위험이 있습니다.
  * EC2 서버 내부에서 접속 정보를 관리하도록 설정합니다.

## RDS 테이블 생성
먼저 RDS 테이블을 생성하겠습니다.   
여기선 JPA가 사용될 엔티티 테이블과 스프링 세션이 사용될 테이블 2가지 종류를 생성합니다.   
JPA가 사용할 테이블은 테스트 코드 수행 시 로그로 생성되는 쿼리를 사용하면 됩니다.   

테스트 코드를 수행하면 다음과 같이 로그가 발생하니 create table부터 복사하여 RDS에 반영합니다.
```
2021-07-13 22:50:25.467 DEBUG 14028 --- [           main] org.hibernate.SQL                        : 
    
    create table car (
       car_id bigint not null auto_increment,
        created_date datetime(6),
        modified_date datetime(6),
        car_number varchar(255),
        category varchar(255),
        color varchar(255),
        model varchar(255),
        production_year varchar(255),
        purchase_date datetime(6),
        release_date datetime(6),
        staff varchar(255),
        status varchar(255),
        vin varchar(255),
        primary key (car_id)
    ) engine=InnoDB
```

스프링 세션 테이블은 schema-mysql.sql 파일에서 확인할 수 있습니다.   
File 검색(Ctrl + Shift + N)으로 찾습니다.   
![1]()   

해당 파일에는 다음과 같은 세션 테이블이 있습니다.   
```
CREATE TABLE SPRING_SESSION (
	PRIMARY_ID CHAR(36) NOT NULL,
	SESSION_ID CHAR(36) NOT NULL,
	CREATION_TIME BIGINT NOT NULL,
	LAST_ACCESS_TIME BIGINT NOT NULL,
	MAX_INACTIVE_INTERVAL INT NOT NULL,
	EXPIRY_TIME BIGINT NOT NULL,
	PRINCIPAL_NAME VARCHAR(100),
	CONSTRAINT SPRING_SESSION_PK PRIMARY KEY (PRIMARY_ID)
) ENGINE=InnoDB ROW_FORMAT=DYNAMIC;

CREATE UNIQUE INDEX SPRING_SESSION_IX1 ON SPRING_SESSION (SESSION_ID);
CREATE INDEX SPRING_SESSION_IX2 ON SPRING_SESSION (EXPIRY_TIME);
CREATE INDEX SPRING_SESSION_IX3 ON SPRING_SESSION (PRINCIPAL_NAME);

CREATE TABLE SPRING_SESSION_ATTRIBUTES (
	SESSION_PRIMARY_ID CHAR(36) NOT NULL,
	ATTRIBUTE_NAME VARCHAR(200) NOT NULL,
	ATTRIBUTE_BYTES BLOB NOT NULL,
	CONSTRAINT SPRING_SESSION_ATTRIBUTES_PK PRIMARY KEY (SESSION_PRIMARY_ID, ATTRIBUTE_NAME),
	CONSTRAINT SPRING_SESSION_ATTRIBUTES_FK FOREIGN KEY (SESSION_PRIMARY_ID) REFERENCES SPRING_SESSION(PRIMARY_ID) ON DELETE CASCADE
) ENGINE=InnoDB ROW_FORMAT=DYNAMIC;
```

이것 역시 복사하여 RDS에 반영합니다.   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)