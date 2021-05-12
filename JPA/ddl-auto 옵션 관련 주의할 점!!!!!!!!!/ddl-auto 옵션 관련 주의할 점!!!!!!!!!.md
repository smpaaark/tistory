얼마전에 개발바닥 호돌맨님의 [재난급 서버 장애내고 개발자 인생 끝날뻔 한 썰](https://youtu.be/SWZcrdmmLEU)을 보게 되었다.    
나처럼 JPA를 많이 안써본 사람들이 보면 아주 도움될 영상이다🤣🤣   
결론은 ***spring.jpa.hibernate.ddl-auto: create*** 옵션은 로컬환경에서만 사용해야 된다는 것이다.   
모르시는 분들을 위해 설명하자면 create 옵션은 해당하는 테이블이 있으면 DROP하고 새로 만들어 버린다.   

## 관련 로그
```
2021-04-28 15:58:50.411 DEBUG 32520 --- [           main] org.hibernate.SQL                        : 
    
    drop table if exists member CASCADE 
2021-04-28 15:58:50.417 DEBUG 32520 --- [           main] org.hibernate.SQL                        : 
    
    drop sequence if exists hibernate_sequence
2021-04-28 15:58:50.423 DEBUG 32520 --- [           main] org.hibernate.SQL                        : create sequence hibernate_sequence start with 1 increment by 1
2021-04-28 15:58:50.424 DEBUG 32520 --- [           main] org.hibernate.SQL                        : 
    
    create table member (
       id bigint not null,
        username varchar(255),
        primary key (id)
    )
```

## ddl-auto 옵션 종류
* create: 기존의 테이블이 있다면 지우고 새로 만든다.
* create-drop: 테이블을 새로 만들고 앱이 종료될 때 지운다.
* update: 변경된 스키마를 적용한다.
* validate: 변경된 스키마가 있는지 확인만 하고 만약 변경이 있다면 오류가 발생한다.