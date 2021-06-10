김영한님의 [자바 ORM 표준 JPA 프로그래밍-기본편](https://inf.run/4hCd) 강의를 들으며 실습하는 중   
ddl-auto 옵션이 create인 상태에서 main을 실행했는데 테이블 drop할때 에러가 발생했다.

## 에러 내용
```
WARN: GenerationTarget encountered exception accepting command : Error executing DDL "
    drop table Item if exists" via JDBC Statement
...
Caused by: org.h2.jdbc.JdbcSQLSyntaxErrorException: Cannot drop "ITEM" because "FK5SQ6D5AGRC34ITHPDFS0UMO9G" depends on it; SQL statement:

    drop table Item if exists [90107-200]
...
```

## 원인
현재 사용중인 H2의 버전이 1.4.200인데 해당 버전은 Hibernate 5.4.13.Final 부터 정상 호환된다고 한다.   
역시나 현재 Hibernate의 버전이 5.3.10 Final 버전이었다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/JPA/ddl-auto%EA%B0%80%20create%EC%9D%B8%EB%8D%B0%20%ED%85%8C%EC%9D%B4%EB%B8%94%20drop%EC%8B%9C%20%EC%97%90%EB%9F%AC%20%EB%B0%9C%EC%83%9D/1.PNG)

## 해결
### pom.xml에 hibernate 버전 수정
```
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>5.4.13.Final</version>
</dependency>
```
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/JPA/ddl-auto%EA%B0%80%20create%EC%9D%B8%EB%8D%B0%20%ED%85%8C%EC%9D%B4%EB%B8%94%20drop%EC%8B%9C%20%EC%97%90%EB%9F%AC%20%EB%B0%9C%EC%83%9D/2.PNG)

### 테스트 코드
```
public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

    EntityManager em = emf.createEntityManager();
    EntityTransaction tx = em.getTransaction();
    tx.begin();

    try {
        Movie movie = new Movie();
        movie.setDirector("aaaa");
        movie.setActor("bbbb");
        movie.setName("바람과함께사라지다");
        movie.setPrice(10000);

        em.persist(movie);
        
        em.flush();
        em.clear();

        Movie findMovie = em.find(Movie.class, movie.getId());
        System.out.println("findMovie = " + findMovie);

        tx.commit();
    } catch (Exception e) {
        tx.rollback();
    } finally {
        em.close();
    }

    emf.close();
}
```

### 에러 없이 정상 작동
```
Hibernate: 
    select
        movie0_.id as id2_2_0_,
        movie0_1_.name as name3_2_0_,
        movie0_1_.price as price4_2_0_,
        movie0_.actor as actor1_6_0_,
        movie0_.director as director2_6_0_ 
    from
        Movie movie0_ 
    inner join
        Item movie0_1_ 
            on movie0_.id=movie0_1_.id 
    where
        movie0_.id=?
findMovie = hellojpa.Movie@5cbb84b1
```