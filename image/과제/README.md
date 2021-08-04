# [우아한형제들]Java-Backend 프로젝트 과제_박성민
## 기술 스택
### 주문 & 라이더 시스템 공통
* Java 11
* Maven
* MySQL, H2
* Docker
* Spring Data Jpa, QueryDsl
* JUnit5
* Flyway
* Spring Rest Docs

### 라이더 시스템에서만 사용
* Spring Batch
* Quartz

## 주문 시스템
### API 목록
* 주문 등록 - 주문생성 프로그램에서 호출
  * 외부의 주문생성 프로그램에서 주문 등록 API를 통해 주문을 발생시킵니다. 
  * 주문 등록이 성공할 경우 라이더 시스템에 배달 등록 요청을 합니다.
    * 바로 배달 가능한 라이더가 있을 경우 주문 상태는 PICKUP으로 저장됩니다.   
    * 바로 배달 가능한 라이더가 없는 경우에는 주문 상태는 RECEIPT로 저장됩니다.    
    
    ![주문등록](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%A3%BC%EB%AC%B8%EB%93%B1%EB%A1%9D.PNG)   
  * 라이더 시스템의 배달 등록까지 성공한 경우에만 주문 등록이 성공합니다.
* 주문 업데이트(PICKUP, COMPLETE) - 라이더시스템이 호출
  * 라이더 시스템이 RECEIPT 상태인 주문을 PICKUP으로 변경 요청
  * 라이더 시스템이 PICKUP 상태인 주문을 COMPLETE로 변경 요청
* 주문 조회

### API 문서 접속 정보
주문 시스템 실행 후 접속해야 합니다.   
http://localhost:8080/docs/index.html   
![주문API](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%A3%BC%EB%AC%B8API.PNG)

## 설계
### 엔티티 설계
![Order_엔티티](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Ordrer_%EC%97%94%ED%8B%B0%ED%8B%B0.PNG)   
하나의 주문은 여러개의 메뉴를 가질 수 있으므로 메뉴와 주문은 다대다 관계입니다.   
주문메뉴라는 엔티티를 추가해서 다대다 관계를 일대다, 다대일 관계로 풀어냈습니다. 
* **메뉴(Menu)**
  * **id**, **메뉴 번호(menuNo)**, **메뉴명(menuName)**, **가격(price)**, **재고 수량(stockQuantity)** 를 갖고 있습니다.
  * 주문 등록이 성공하면 **재고 수량(stockQuantity)** 이 줄어듭니다.   
  * **주문메뉴(OrderMenu)** 가 **메뉴(Menu)** 를 참조하는 것으로 충분히 구현 가능하다고 판단하여 **주문메뉴(OrderMenu)** 와 양방향 연관관계 설정을 하지 않았습니다.
* **주문(Order)**
  * **id**, **주문 번호(orderNo)**, **가게 번호(shopNo)**, **배달 거리(distance)**, **주문 일자(acceptTime)**, **총 주문 가격(totalPrice)**, **주문 상태(orderStatus)**, **주문 메뉴(List\<OrderMenu> menus)** 를 갖고 있습니다.
  * **가게 번호(shopNo)** 가 "SHOP_01" 고정값이라는 요구 사항에 의해 가게 테이블은 별도로 설계하지 않았습니다.
  * **주문 일자(acceptTime)** 는 JPA Auditing을 사용하여 엔티티가 생성되어 저장될 때 자동 저장되게 했습니다.
  * **주문 상태(orderStatus)** 는 Enum 클래스를 사용했습니다. (RECEIPT, PICKUP, COMPLETE, CANCEL)
  * 한 번 주문 시 여러 메뉴를 주문할 수 있으므로 **주문(Order)** 과 **주문 메뉴(OrderMenu)** 는 일대다 관계를 갖습니다.   
  * 주문 등록 실패 시 **총 주문 가격(totalPrice)** 은 0, **주문 상태(orderStatus)** 는 CANCEL로 처리됩니다.   
    * 과제 요구사항에 기재된 주문 실패 조건에만 CANCEL상태로 DB에 저장되도록 했습니다.
    * 필수값 오류 및 과제 요구사항에 기재된 주문 실패 조건 외의 유효성 검증에서 실패할 경우에는 DB에 저장되지 않습니다.
  * 주문 등록 성공 시 **총 주문 가격(totalPrice)** 이 계산되어 저장되고, **주문 상태(orderStatus)** 는 RECEIPT 또는 PIKCUP으로 저장됩니다.
  * 라이더 시스템에서 호출하는 주문 업데이트 성공 시 **주문 상태(orderStatus)** 가 변경됩니다. (PICKUP, COMPLETE)
* **주문 메뉴(OrderMenu)**
  * **id**, **메뉴번호(menuNo)**, **수량(quantity)**, **주문(Order order)**, **메뉴(Menu menu)** 를 갖고 있습니다.
  * 한 번 주문 시 여러 메뉴를 주문할 수 있으므로 **주문 메뉴(OrderMenu)** 와 **주문(Order)** 은 다대일 관계를 갖습니다.  
  * 위와 같은 이유로 **주문 메뉴(OrderMenu)** 와 **메뉴(Menu)** 도 다대일 관계를 갖습니다.  
* **공통**
  * 모든 엔티티의 **생성 일자(createdDate)** 와 **수정 일자(modifiedDate)** 를 저장하기 위해 JPA Auditing을 사용했습니다.

### 테이블 설계
![Order_ERD](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Order_ERD.png)   
* **메뉴(MENU)**
  * **id(MENU_ID)**, **메뉴 번호(MENU_NO)**, **메뉴명(MENU_NAME)**, **가격(PRICE)**, **재고 수량(STOCK_QUANTITY)** 을 갖고 있습니다.
  * **메뉴 번호(MENU_NO)** 는 유일한 값이기 때문에 UniqueKey로 설정했습니다.
* **주문(ORDERS)**
  * 데이터베이스가 ORDER BY를 예약어로 잡고 있는 경우가 많아서 ORDERS를 사용했습니다.
  * **id(ORDER_ID)**, **주문 번호(ORDER_NO)**, **가게 번호(SHOP_NO)**, **배달 거리(DISTANCE)**, **주문 일자(ACCEPT_TIME)**, **총 주문 가격(TOTAL_PRICE)**, **주문 상태(ORDER_STATUS)** 를 갖고 있습니다.
  * **주문 번호(ORDER_NO)** 는 유일한 값이기 때문에 UniqueKey로 설정했습니다.
  * **생성 일자(CREATED_DATE)** 는 DB 관점의 데이터 생성 일자로 사용하고, **주문 일자(ACCEPT_TIME)** 를 따로 추가했습니다.
  * **주문 상태(ORDER_STATUS)** 는 Enum 값을 String 형태로 저장되도록 했습니다.
* **주문 메뉴(ORDER_MENU)** 
  * **id(ORDER_MENU_ID)**, **주문 ID(ORDER_ID)**, **메뉴 ID(MENU_ID)**, **메뉴 번호(MENU_NO)**, **수량(QUANTITY)** 를 갖고 있습니다.
  * **주문 ID(ORDER_ID)** 와 **메뉴 ID(MENU_ID)** 는 ForeignKey 입니다.
* **공통**
  * **id** 는 PrimaryKey이며, AUTO_INCREMENT로 설정했습니다.
  * **생성 일자(CREATED_DATE)** 와 **수정 일자(MODIFIED_DATE)** 가 자동으로 저장됩니다.
  
### 연관관계 설계
* **주문 메뉴(ORDER_MENU)** 와 **주문(ORDERS)** 
  * 다대일 양방향 관계입니다.
  * 외래 키가 **주문 메뉴(ORDER_MENU)** 에 있으므로 **주문 메뉴(ORDER_MENU)** 를 연관관계의 주인으로 설정했습니다.
  * 그러므로 **OrderMenu.order** 를 **ORDER_MENU.ORDER_ID** 외래 키와 매핑했습니다.
* **주문 메뉴(ORDER_MENU)** 와 **메뉴(MENU)**
  * 다대일 단방향 관계입니다.
  * **OrderMenu.menu** 를 **ORDER_MENU.MENU_ID** 외래 키와 매핑했습니다.

## 주문 실패 조건
* DB에 저장하지 않고 실패 처리하는 경우
  * 필수값 오류
    * 주문번호(orderNo), 가게번호(shopNo), 주문메뉴(menus) 값이 비어있는 경우
    * 요청 예시
    ```
    {
        "orderNo": null,
        "shopNo": null,
        "distance": 0,
        "menus": null
    }
    ```
    * 응답 예시
    ```
    {
        "status": "400",
        "message": "REQUIRED_FIELD_EMPTY_ERROR",
        "responseDate": "20210803143026",
        "data": {
            "orderNo": null,
            "shopNo": null,
            "acceptTime": "20210803143025",
            "totalPrice": 0,
            "result": "CANCEL"
        }
    }
    ```
  * 중복된 주문 번호(orderNo)로 요청된 경우
    * 응답 예시
    ```
    {
        "status": "400",
        "message": "DUPLICATED_ORDER_NO_ERROR",
        "responseDate": "20210803144132",
        "data": {
            "orderNo": "ORD_A01",
            "shopNo": "SHOP_01",
            "acceptTime": "20210803144132",
            "totalPrice": 0,
            "result": "CANCEL"
        }
    }
    ```
  * 라이더 시스템에 배달 등록 요청 시 성공하지 못한 경우
    * 응답 예시
    ```
    {
        "status": "500",
        "message": "EXTERNAL_API_FAIL_ERROR",
        "responseDate": "20210803144659",
        "data": ""
    }
    ```
* DB에 CANCEL 상태로 저장되는 경우(요구 사항에 기재된 주문 실패 조건)   
  ![주문등록실패_DB](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%A3%BC%EB%AC%B8%EB%93%B1%EB%A1%9D%EC%8B%A4%ED%8C%A8_DB.PNG)
  * 주문번호(orderNo)가 10자리보다 긴 경우
  * 가게번호(shopNo)가 "SHOP_01"이 아닌 경우
  * 주문 메뉴 내에 중복된 메뉴가 있을 경우
  * 각 메뉴의 수량이 1~5개가 아닌 경우
  * 메뉴판에 존재하지 않는 메뉴가 있을 경우
  * 메뉴의 재고 수량이 부족할 경우
  * 배달 거리가 200~600내의 100 단위로 입력되지 않은 경우
  * 요청 예시
  ```
  {
      "orderNo": "ORD_A010101",
      "shopNo": "SHOP_02",
      "distance": 100,
      "menus": [
          {
              "menuNo": "MENU_01",
              "quantity": 1
          },
          {
              "menuNo": "MENU_02",
              "quantity": 5
          },
          {
              "menuNo": "MENU_01",
              "quantity": 6
          },
          {
              "menuNo": "MENU_99",
              "quantity": 1
          }
      ]
  }
  ```
  * 응답 예시
  ```
  {
      "status": "400",
      "message": "VALIDATION_ERROR",
      "responseDate": "20210803150950",
      "data": {
          "orderNo": "ORD_A010101",
          "shopNo": "SHOP_02",
          "acceptTime": "20210803150950",
          "totalPrice": 0,
          "result": "CANCEL"
      }
  }
  ```

## 라이더 시스템
### API 목록
* 배달 등록 - 주문 시스템이 호출
  * 현재 배달 가능한 라이더가 있을 경우 배달 등록 후 배달 상태 PICKUP으로 응답
  ```
  {
      "status": "201",
      "message": "CREATED",
      "responseDate": "20210804165631",
      "data": {
          "orderNo": "ORD_A01",
          "deliveryStatus": "PICKUP"
      }
  }
  ```
  * 현재 배달 가능한 라이더가 없을 경우 배달 등록 후 배달 상태 READY 로 응답
  ```
  {
      "status": "201",
      "message": "CREATED",
      "responseDate": "20210804165755",
      "data": {
          "orderNo": "ORD_A01",
          "deliveryStatus": "READY"
      }
  }
  ```
* 배달 조회

### 배치 기능
Spring Batch와 Quartz를 사용하여 구현했습니다.
* 라이더 이동   
10초마다 라이더를 이동 시킵니다.   
  * 라이더 배달 진행중   

  ![Rider_완료전](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_%EC%99%84%EB%A3%8C%EC%A0%84.PNG)    
  ```
  2021-08-04 17:01:06.608  INFO 10160 --- [   scheduling-1] c.s.r.b.job.DeliveryJobConfiguration     : 

  === 라이더 이동 ===
  * 라이더코드: RIDER_A01
  * 라이더명: 임꺽정
  * 주문번호: ORD_A05
  * 남은배송거리: 400
  ```
  * 라이더 이동 완료      
  배달이 완료되면 주문 시스템에 주문 업데이트 API 요청을 합니다. 
    
  ![Rider_완료](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_%EC%99%84%EB%A3%8C.PNG)
  ```
  2021-08-04 17:01:28.323  INFO 10160 --- [   scheduling-1] c.s.r.b.job.DeliveryJobConfiguration     : 

  === 라이더 이동 ===
  * 라이더코드: RIDER_A01
  * 라이더명: 임꺽정
  * 주문번호: ORD_A05
  * 남은배송거리: 0

  2021-08-04 17:01:28.575  INFO 10160 --- [   scheduling-1] c.s.r.b.job.DeliveryJobConfiguration     : 

  === 배달 완료 요청 ===
  * 주문번호: ORD_A05
  * 요청 데이터: {"orderStatus":"COMPLETE"}
  * 응답 데이터: {"status":"200","message":"OK","responseDate":"20210804170128","data":{"orderNo":"ORD_A05","acceptTime":"20210804170029","orderStatus":"COMPLETE"}}
  ```

* 대기중인 배달 픽업   
10초마다 READY 상태인 배달 주문이 있고, 배달 가능한 라이더가 있을 경우 PICKUP합니다.   
PICKUP할 때 주문 시스템에 주문 업데이트 API 요청을 합니다.
  * 배달 주문 대기중   

  ![Rider_배달대기](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_%EB%B0%B0%EB%8B%AC%EB%8C%80%EA%B8%B0.PNG)   
  * 배달 주문 픽업   

  ![Rider_픽업](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_%ED%94%BD%EC%97%85.PNG)     
  ```
  === 주문 픽업 ===
  * 라이더코드: RIDER_A01
  * 라이더명: 임꺽정
  * 주문번호: ORD_A03
  * 남은배송거리: 500

  2021-08-04 18:00:35.525  INFO 12416 --- [   scheduling-1] c.s.r.b.job.DeliveryJobConfiguration     : 

  === 주문 픽업 요청 ===
  * 주문번호: ORD_A03
  * 요청 데이터: {"orderStatus":"PICKUP"}
  * 응답 데이터: {"status":"200","message":"OK","responseDate":"20210804180035","data":{"orderNo":"ORD_A03","acceptTime":"20210804175950","orderStatus":"PICKUP"}}
  ```

### API 문서 접속 정보
라이더 시스템 실행 후 접속해야 합니다.   
http://localhost:8081/docs/index.html    
![라이더API](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%9D%BC%EC%9D%B4%EB%8D%94API.PNG)

## 설계
### 엔티티 설계
![Rider_엔티티](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_%EC%97%94%ED%8B%B0%ED%8B%B02.PNG)   
한 명의 라이더는 여러개의 배달을 할 수 있으므로 배달과 라이더는 다대일 관계입니다.
* **라이더(Rider)**
  * **id**, **라이더코드(riderCode)**, **라이더 명(riderName)**, **배달 수단(deliveryMethod)**, **이동 속도(speed)**, **라이더 상태(riderStatus)**를 갖고 있습니다.
  * **배달 수단(deliveryMethod)** 은 과제 요구사항에서 2개의 종류만 존재한다고 하여 따로 엔티티로 설계하지 않고 Enum 클래스로 생성했습니다. (MOTORCYCLE(100), BICYCLE(50))   
  * **라이더 상태(riderStatus)** 도 Enum 클래스로 생성했습니다. (READY, PICKUP)
    * 배달을 시작하면 PICKUP으로 변경되고, 배달이 완료되면 READY로 변경됩니다.
* **배달(Delivery)**
  * **id**, **주문 번호(orderNo)**, **총 주문 금액(totalPrice)**, **라이더코드(riderCode)**, **라이더명(riderName)**, **배달 수단(deliveryMethod)**, **이동 속도(speed)**, **총 배달 거리(totalDistance)**, **남은 배달 거리(remainDistance)**, **배달 상태(deliveryStatus)**, **라이더(Rider rider)** 를 갖고 있습니다.
  * 라이더 이동 배치 기능 및 배달 조회 API에서 **라이더(Rider)** 엔티티 join 없이 **배달(Delivery)** 엔티티만 조회해도 구현되게 하기 위해 필요한 라이더 정보를 갖게 하였습니다.   
    * 해당 데이터들은 라이더가 주문을 픽업할 때 생성됩니다. (배달 상태가 READY 상태일 때는 데이터가 없습니다.)   

    ![Rider_배달대기](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_%EB%B0%B0%EB%8B%AC%EB%8C%80%EA%B8%B0.PNG)   
  * **남은 배달 거리(remainDistance)** 가 0이 되면 주문 시스템으로 주문 업데이트(COMPLETE) 요청을 합니다. 
  * **배달 상태(deliveryStatus)** 는 Enum 클래스로 생성했습니다. (READY, PICKUP, COMPLETE)
* **공통**
  * 모든 엔티티의 **생성 일자(createdDate)** 와 **수정 일자(modifiedDate)** 를 저장하기 위해 JPA Auditing을 사용했습니다.

### 테이블 설계   
![Rider_ERD](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/Rider_ERD.png)   
* **라이더(RIDER)**
  * **id(RIDER_ID)**, **라이더 코드(RIDER_CODE)**, **라이더 명(RIDER_NAME)**, **배달 수단(DELIVERY_METHOD)**, **이동 속도(SPEED)**, **라이더 상태(RIDER_STATUS)** 를 갖고 있습니다.
  * **배달(DELIVERY)** 이 **라이더(RIDER)** 를 참조하는 것으로 충분히 구현 가능하다고 판단하여 **배달(DELIVERY)** 과 양방향 연관관계 설정을 하지 않았습니다.
  * **배달 수단(DELIVERY_METHOD)** 과 **라이더 상태(RIDER_STATUS)** 는 Enum 값을 String 형태로 저장되도록 했습니다.
* **배달(DELIVERY)**
  * **id(DELIVERY_ID)**, **주문 번호(ORDER_NO)**, **총 주문 금액(TOTAL_PRICE)**, **라이더 코드(RIDER_CODE)**, **라이더 명(RIDER_NAME)**, **배달 수단(DELIVERY_METHOD)**, **이동 속도(SPEED)**, **총 배달 거리(TOTAL_DISTANCE)**, **남은 이동 거리(REMAIN_DISTANCE)**, **배달 상태(DELIVERY_STATUS)**, **라이더ID(RIDER_ID)** 을 갖고 있습니다.
  * **배달 수단(DELIVERY_METHOD)** 과 **배달 상태(DELIVERY_STATUS)** 는 Enum 값을 String 형태로 저장되도록 했습니다.
  * **라이더ID(RIDER_ID)** 는 ForeignKey 입니다.
* **공통**
  * **id** 는 PrimaryKey이며, AUTO_INCREMENT로 설정했습니다.
  * **생성 일자(CREATED_DATE)** 와 **수정 일자(MODIFIED_DATE)** 가 자동으로 저장됩니다.

### 연관관계 설계
* **라이더(RIDER)** 와 **배달(DELIVERY)** 
  * 다대일 단방향 관계입니다.
  * 외래 키가 **배달(DELIVERY)** 에 있으므로 **배달(DELIVERY)** 을 연관관계의 주인으로 설정했습니다.
  * 그러므로 **Delivery.rider** 를 **DELIVERY.RIDER_ID** 외래 키와 매핑했습니다.

## 로그 확인 방법
로그는 프로젝트 폴더 내에 log/app.log에 저장됩니다.   
![로그파일경로]()
* 라이더 이동 로그   
아래와 같은 형식으로 라이더 이동 로그를 출력합니다.
```
2021-08-04 22:12:39.967 [scheduling-1] INFO  c.s.r.b.job.DeliveryJobConfiguration - 

=== 라이더 이동 ===
* 라이더코드: RIDER_A01
* 라이더명: 임꺽정
* 주문번호: ORD_A01
* 남은배송거리: 200
```

* 배달 완료 로그   
아래와 같은 형식으로 배달 완료 로그를 출력합니다.
```
2021-08-04 22:13:02.616 [scheduling-1] INFO  c.s.r.b.job.DeliveryJobConfiguration - 

=== 배달 완료 요청 ===
* 주문번호: ORD_A01
* 요청 데이터: {"orderStatus":"COMPLETE"}
* 응답 데이터: {"status":"200","message":"OK","responseDate":"20210804221302","data":{"orderNo":"ORD_A01","acceptTime":null,"orderStatus":"COMPLETE"}}
```

## 실행 방법
### 도커로 MySQL 설치 후 실행하기
도커가 설치되어 있다는 가정하에 진행하겠습니다.  
1. Mysql 이미지를 설치합니다.
```
docker pull mysql
```
![도커_이미지설치](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%9D%B4%EB%AF%B8%EC%A7%80%EC%84%A4%EC%B9%98.PNG)   

2. Mysql 이미지가 잘 설치되었는지 확인합니다.
```
docker images
```
![도커_이미지확인](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%9D%B4%EB%AF%B8%EC%A7%80%ED%99%95%EC%9D%B8.PNG)   

3. Mysql 컨테이너 실행
```
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -d mysql
```
![도커_컨테이너실행](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%8B%A4%ED%96%89.PNG)

4. 컨테이너 상태 조회
```
docker ps -a
```
![도커_컨테이너상태조회](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%83%81%ED%83%9C%EC%A1%B0%ED%9A%8C.PNG)

방금 실행한 mysql의 STATUS가 Up이면 정상적으로 실행된 것 입니다.
> 만약 Exited 상태라면 다음 명령어를 통해 실행시켜줍니다.
```
docker start [컨테이너ID 또는 컨테이너명]
```

6. Mysql 컨테이너 접속
```
docker exec -i -t mysql bash
```
![도커_컨테이너접속](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%A0%91%EC%86%8D.PNG)

* 사용자 계정 입력
```
mysql -u root -p
```
![사용자계정입력](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%82%AC%EC%9A%A9%EC%9E%90%EA%B3%84%EC%A0%95%EC%9E%85%EB%A0%A5.PNG)

* 패스워드 입력
> 3번 과정에서 패스워드를 password로 설정했으므로 password라고 입력합니다.    

![도커_접속성공](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%A0%91%EC%86%8D%EC%84%B1%EA%B3%B5.PNG)

7. 데이터베이스 생성
```
create database order_system;
create database rider_system;
```
![도커_DB생성](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_DB%EC%83%9D%EC%84%B1.PNG)   

8. 사용자 계정 추가
ID는 smpaaark, 패스워드는 smpaaark1로 추가합니다.
```
CREATE USER 'smpaaark'@'%' IDENTIFIED BY 'smpaaark1';
```
![도커_사용자추가](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EC%82%AC%EC%9A%A9%EC%9E%90%EC%B6%94%EA%B0%80.PNG)   

9. 모든 권한 부여
```
GRANT ALL PRIVILEGES ON *.* TO 'smpaaark'@'%';
flush privileges;
```
![도커_권한부여](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%EA%B6%8C%ED%95%9C%EB%B6%80%EC%97%AC.PNG)

10. 서버 타임존 설정
```
SET GLOBAL time_zone='Asia/Seoul';
set time_zone='Asia/Seoul';
```
![도커_타임존](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%ED%83%80%EC%9E%84%EC%A1%B4.PNG)

11. 서버 타임존 설정 확인
```
SELECT @@global.time_zone, @@session.time_zone, @@time_zone;
```
![도커_타임존확인](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%8F%84%EC%BB%A4_%ED%83%80%EC%9E%84%EC%A1%B4%ED%99%95%EC%9D%B8.PNG)

### 애플리케이션 빌드 및 실행
주문 시스템과 라이더 시스템 빌드 과정은 똑같으며, 애플리케이션 실행 시 명령어만 다릅니다.   
1. OS에 맞는 명령행을 실행합니다.
2. 각 시스템 폴더로 이동합니다.
  * Window(cmd)
  ```
  cd "주문 시스템 경로"
  ```
  ![빌드_경로이동](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%B9%8C%EB%93%9C_%EA%B2%BD%EB%A1%9C%EC%9D%B4%EB%8F%99.PNG)   
  * Mac/Linux (Git Bash를 사용했습니다.)
  ```
  cd 주문 시스템 경로
  ```
  ![빌드_경로2](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%B9%8C%EB%93%9C_%EA%B2%BD%EB%A1%9C2.PNG)   
3. 애플리케이션 빌드
  * Window
  ```
  mvnw clean package
  ```
  ![빌드_성공](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%B9%8C%EB%93%9C_%EC%84%B1%EA%B3%B5.PNG)   
  * Mac/Linux
  ```
  ./mvnw clean package
  ```
  ![빌드_성공2](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EB%B9%8C%EB%93%9C_%EC%84%B1%EA%B3%B52.PNG)   
4. 애플리케이션 실행
  * **주문 시스템**
  ```
  java -jar target/order-system-0.0.1-SNAPSHOT.jar
  ```
  * Window   

  ![애플리케이션_실행](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98_%EC%8B%A4%ED%96%89.PNG)
  
  * Mac/Linux   

  ![애플리케이션_실행2](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98_%EC%8B%A4%ED%96%892.PNG)      

  * **라이더 시스템**
  ```
  java -jar -Dspring.profiles.active=real target/rider-system-0.0.1-SNAPSHOT.jar
  ```
  * Window   

  ![애플리케이션_실행3](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98_%EC%8B%A4%ED%96%893.PNG)      

  * Mac/Linux   
  
  ![애플리케이션_실행4](https://raw.githubusercontent.com/smpark1020/tistory/master/image/%EA%B3%BC%EC%A0%9C/%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98_%EC%8B%A4%ED%96%894.PNG)
