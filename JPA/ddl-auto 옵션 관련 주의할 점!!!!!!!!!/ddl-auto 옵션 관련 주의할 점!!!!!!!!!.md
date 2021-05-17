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
* create: 기존테이블 삭제 후 다시 생성 (DROP + CREATE)
* create-drop: create와 같으나 종료시점에 테이블 DROP
* update: 변경분만 반영```(운영DB에서는 사용하면 안됨)```
* validate: 엔티티와 테이블이 정상 매핑되었는지만 확인
* none: 사용하지 않음(사실상 없는 값이지만 관례상 none이라고 한다.)

## 주의할 점
* 운영 장비에서는 절대 crate, create-drop, update 사용하면 안된다.
* 개발 초기 단계는 create 또는 update
* 테스트 서버는 update 또는 validate
* 스테이징과 운영 서버는 validate 또는 none
하지만 로컬 환경을 제외한 나머지 서버에서는 최대한 직접 쿼리를 날려서 적용하는 것이 가장 좋다.