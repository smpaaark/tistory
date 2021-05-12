실무에서 데이터의 생성 시간과 수정 시간이 모든 테이블에 필수적으로 존재해야 한다.      
하지만 개발할때 무언가를 생성하거나 수정할때마다 이 컬럼들을 신경쓰는 것은 매우 귀찮은 일이다.   
JPA Auditing을 사용하면 생성 시간과 수정 시간을 자동화 할 수 있다.

## BaseTimeEntity 생성
```
package com.usedcar.admin.domain;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseTimeEntity {

    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;

}
```
* \@MappedSuperclass: Entitiy들이 해당 클래스를 상속할 경우 정의된 필드도 컬럼으로 인식되도록 한다.
* \@EntityListeners(AuditingEntityListener.class): BaseTimeEntity 클래스에 Auditing 기능을 포함시킨다.
* \@CreatedDate: Enitity가 생성되어 저장될 때 시간이 자동 저장된다.
* \@LastModifiedDate: 조회한 Entity의 값을 변경할 때 시간이 자동 저장된다.

## Entity 클래스는 BaseTimeEntity를 상속받아야 한다.
```
@Getter
@NoArgsConstructor
@Entity
public class Car extends BaseTimeEntity {
    ...
}
```

## 메인 클래스에 JPA Auditing를 활성화 해주는 어노테이션을 추가한다.
```
@EnableJpaAuditing //JPA Auditing 활성화
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

## 테스트 코드로 확인
```
@Test
@Rollback(value = false)
public void 차량매입_불러오기() throws Exception {
    // given
    String carNumber = "04구4716";
    String vin = "12345678";
    Category category = Category.DOMESTIC;
    String model = "더 뉴 K5";
    String color = "검정";
    String productionYear = "2018";
    LocalDateTime purchaseDate = LocalDateTime.now();

    carRepository.save(Car.builder()
            .carNumber(carNumber)
            .vin(vin)
            .category(category)
            .model(model)
            .color(color)
            .productionYear(productionYear)
            .purchaseDate(purchaseDate)
            .build());

    // when
    List<Car> carList = carRepository.findAll();

    // then
    Car car = carList.get(0);
    assertThat(car.getCarNumber()).isEqualTo(carNumber);
    assertThat(car.getVin()).isEqualTo(vin);
    assertThat(car.getCategory()).isEqualTo(category);
    assertThat(car.getModel()).isEqualTo(model);
    assertThat(car.getColor()).isEqualTo(color);
    assertThat(car.getProductionYear()).isEqualTo(productionYear);
    assertThat(car.getPurchaseDate()).isNotNull();
    assertThat(car.getCreatedDate()).isAfter(purchaseDate); //createdDate
    assertThat(car.getModifiedDate()).isAfter(purchaseDate); //modifiedDate
}
```

### 결과
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/JPA/JPA%20Auditing%EC%9C%BC%EB%A1%9C%20%EC%83%9D%EC%84%B1%EC%8B%9C%EA%B0%84%2C%20%EC%88%98%EC%A0%95%EC%8B%9C%EA%B0%84%20%EC%9E%90%EB%8F%99%ED%99%94%ED%95%98%EA%B8%B0/1.PNG)

## 테이블 확인
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/JPA/JPA%20Auditing%EC%9C%BC%EB%A1%9C%20%EC%83%9D%EC%84%B1%EC%8B%9C%EA%B0%84%2C%20%EC%88%98%EC%A0%95%EC%8B%9C%EA%B0%84%20%EC%9E%90%EB%8F%99%ED%99%94%ED%95%98%EA%B8%B0/2.PNG)

## 참고
* [스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)