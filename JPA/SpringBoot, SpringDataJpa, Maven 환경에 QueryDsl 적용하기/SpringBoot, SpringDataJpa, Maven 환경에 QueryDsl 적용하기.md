## 1. Maven 설정
의존성 추가
```
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-apt</artifactId>
</dependency>
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-jpa</artifactId>
</dependency>
```
플러그인 추가
```
<plugin>
    <groupId>com.mysema.maven</groupId>
    <artifactId>apt-maven-plugin</artifactId>
    <version>1.1.3</version>
    <executions>
        <execution>
            <goals>
                <goal>process</goal>
            </goals>
            <configuration>
                <outputDirectory>src/main/generated</outputDirectory>
                <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## 2. Java Config 설정
JPAQueryFactory를 주입받을 수 있게 하기 위해 빈으로 등록한다.
```
@Configuration
public class QueryDslConfig {

    @PersistenceContext
    private EntityManager entityManager;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(entityManager);
    }

}
``
```

## 3. CustomRepsitory 생성
```
public interface CarRepositoryCustom {

    public List<Car> findByCarNumber(String carNumber); //테스트할 메서드

}
```

## 4. CustomRepsitoryImpl 생성
JPAQueryFacktory를 생성자 주입을 통해 주입받는다.
CustomRepository의 메서드를 오버라이딩하여 실제 QueryDsl를 사용한 쿼리 관련 코딩을 여기에 작성한다.
```
@RequiredArgsConstructor
public class CarRepositoryImpl implements CarRepositoryCustom {

    private final JPAQueryFactory queryFactory;

    @Override
    public List<Car> findByCarNumber(String carNumber) {
        return queryFactory.selectFrom(car)
                .where(car.carNumber.eq(carNumber))
                .fetch();
    }

}
```

## 5. 기본 도메인 Repository 클래스에서 CustomRepository를 상속받는다.
```
public interface CarRepository extends JpaRepository<Car, Long>, CarRepositoryCustom {

    
}
```

## 테스트
테스트 코드
```
@Test
public void QueryDsl_테스트() throws Exception {
    // given
    String carNumber = "04구4716";
    String vin = "12345678";
    Category category = Category.DOMESTIC;
    String model = "더 뉴 K5";
    String color = "검정";
    String productionYear = "2018";
    LocalDateTime purchaseDate = LocalDateTime.of(2021, 05, 12, 0, 0);
    String staff = "박성민";

    Long id = carRepository.save(getCar(carNumber, vin, category, model, color, productionYear, purchaseDate, staff)).getId();

    // when
    List<Car> cars = carRepository.findByCarNumber(carNumber);

    // then
    assertThat(cars.size()).isEqualTo(1);
    assertThat(cars.get(0).getCarNumber()).isEqualTo(carNumber);
}
```

쿼리 로그
```
2021-05-27 13:34:49.286 DEBUG 55640 --- [           main] org.hibernate.SQL                        : 
    select
        car0_.car_id as car_id1_0_,
        car0_.created_date as created_2_0_,
        car0_.modified_date as modified3_0_,
        car0_.car_number as car_numb4_0_,
        car0_.category as category5_0_,
        car0_.color as color6_0_,
        car0_.model as model7_0_,
        car0_.production_year as producti8_0_,
        car0_.purchase_date as purchase9_0_,
        car0_.release_date as release10_0_,
        car0_.staff as staff11_0_,
        car0_.status as status12_0_,
        car0_.vin as vin13_0_ 
    from
        car car0_ 
    where
        car0_.car_number=?
```

테스트 결과   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/JPA/SpringBoot%2C%20SpringDataJpa%2C%20Maven%20%ED%99%98%EA%B2%BD%EC%97%90%20QueryDsl%20%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0/1.PNG)

## 참고
* [이동욱님 블로그, Spring Boot Data Jpa 프로젝트에 Querydsl 적용하기](https://jojoldu.tistory.com/372)