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
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BEC2%5D%20EC2%20%EC%84%9C%EB%B2%84%EC%97%90%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0%204%20-%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%B6%80%ED%8A%B8%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A1%9C%20RDS%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0/1.PNG)   

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

## 프로젝트 설정
먼저 MariaDB 드라이버를 pom.xml에 등록합니다.   
```
<dependency>
  <groupId>org.mariadb.jdbc</groupId>
  <artifactId>mariadb-java-client</artifactId>
</dependency>
```

그리고 서버에서 구동될 환경을 하나 구성합니다.   
src/main/resources/에 application-real.properties 파일을 추가합니다.   
앞에서 이야기한 대로 application-real.properties로 파일을 만들면 profile=real인 환경이 구성된다고 보면 됩니다.   
실제 운영될 환경이기 때문에 보안/로그상 이슈가 될 만한 설정들을 모두 제거하며 RDS 환경 profile 설정이 추가됩니다.   
```
spring.profiles.include=oauth, real-db

# 쿼리 로그 세팅
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL57Dialect
spring.jpa.properties.hibernate.dialect.storage_engine=innodb

# 세션 저장소 jdbc 설정
spring.session.store-type=jdbc
spring.session.jdbc.initialize-schema=always

# UTF-8 세팅
server.servlet.encoding.charset=UTF-8
server.servlet.encoding.force=true
```

모든 설정이 되었다면 깃허브로 푸시합니다.   

## EC2 설정
OAuth와 마찬가지로 RDS 접속 정보도 보호해야 할 정보이니 EC2 서버에 직접 설정 파일을 둡니다.   

app 디렉토리에 application-real-db.properties 파일을 생성합니다.
```
vim ~/app/application-real-db.properties
```

그리고 다음과 같은 내용을 추가합니다.   
```
# 쿼리 로그 세팅
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.SQL=debug
logging.level.org.hibernate.type=trace

# DB 세팅
spring.datasource.hikari.jdbc-url=mariadb://rds주소:포트명(기본은 3306)/database이름
spring.datasource.hikari.username=db계정
spring.datasource.hikari.password=db계정 비밀번호
spring.datasource.hikari.driver-class-name=org.mariadb.jdbc.Driver

# ddl-auto 세팅
spring.jpa.hibernate.ddl-auto=none
```
1. spring.jpa.hibernate.ddl-auto=none
  * JPA로 테이블이 자동 생성되는 옵션을 None(생성하지 않음)으로 지정합니다.
  * RDS에는 실제 운영으로 사용될 테이블이니 절대 스프링 부트에서 새로 만들지 않도록 해야 합니다.
  * 이 옵션을 하지 않으면 자칫 테이블이 모두 새로 생성될 수 있습니다.
  * 주의해야 하는 옵션입니다.

마지막으로 deploy.sh가 real profile을 쓸 수 있도록 다음과 같이 개선합니다.
```
...
nohup java -jar \
        -Dspring.config.location=classpath:/application.properties,/home/ec2-user/app/application-oauth.properties,/home/ec2-user/app/application-real-db.properties,classpath:/application-real.properties \
        -Dspring.profiles.active=real \
        $REPOSITORY/$JAR_NAME 2>&1 &
```
1. -Dspring.profiles.active=real
  * application-real.properties를 활성화시킵니다.
  * application-real.properties의 spring.profiles.include=oauth,real-db 옵션 때문에 real-db 역시 함께 활성화 대상에 포함됩니다.

이렇게 설정된 후 다시 한번 deploy.sh를 실행해 봅니다.   
nohup.out 파일을 열어 다음과 같이 로그가 보인다면 성공적으로 수행된 것입니다.   


## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)