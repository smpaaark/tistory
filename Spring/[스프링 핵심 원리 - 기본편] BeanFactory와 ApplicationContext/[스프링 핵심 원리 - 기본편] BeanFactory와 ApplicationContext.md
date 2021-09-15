![1]()   

## BeanFactory
* 스프링 컨테이너의 최상위 인터페이스입니다.
* 스프링 빈을 관리하고 조회하는 역할을 담당합니다.
* ```getBean()```을 제공합니다.

## ApplicationContext
* BeanFactory 기능을 모두 상속받아서 제공합니다.
* 애플리케이션을 개발할 때 필요한 수 많은 부가기능을 제공합니다.

## ApplicationContext가 제공하는 부가기능
![2]()   
* ```메시지소스를 활용한 국제화 기능```
  * 예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
* ```환경변수```
  * 로컬, 개발, 운영등을 구분해서 처리
* ```애플리케이션 이벤트```
  * 이벤트를 발행하고 구독하는 모델을 편리하게 지원
* ```편리한 리소스 조회```
  * 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

## 정리
* ApplicatonContext는 BeanFactory의 기능을 상속받습니다.
* ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공합니다.
* BeanFactory를 직접 사용할 일은 거의 없습니다. 부가기능이 포함된 ApplicationContext를 사용합니다.
* BeanFactory나 ApplicationContext를 스프링 컨테이너라 합니다.

## 참조
* [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)