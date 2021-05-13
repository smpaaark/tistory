Excepton 발생 시 \@ExceptionHandler를 사용하여 응답 처리를 할 수있다.   

## 사용자 정의 Exception 클래스 생성
차량을 매입할때 이미 존재하는 차량 번호일 경우 발생시킬 Exception 클래스를 생성한다.   
RunTimeException을 상속받고 생성자를 만들어 준다.   
```
public class DuplicatedCarNumberException extends RuntimeException {

    public DuplicatedCarNumberException() {
    }

    public DuplicatedCarNumberException(String message) {
        super(message);
    }

    public DuplicatedCarNumberException(String message, Throwable cause) {
        super(message, cause);
    }

    public DuplicatedCarNumberException(Throwable cause) {
        super(cause);
    }

}
```

## 서비스 로직 구현
```
private void validateDuplicateCar(CarSaveRequestDto requestDto) {
    int count = carRepository.countByCarNumber(requestDto.getCarNumber());
    if (count > 0) {
        throw new DuplicatedCarNumberException("이미 매입되어있는 차량입니다.(차량번호: " + requestDto.getCarNumber() + ")");
    }
}
```

## ExceptionController 생성
DuplicatedCarNumberException 발생 시 duplicatedCarNumberHandler 메서드가 호출되고,   
그 외 정의하지 않은 Exception 발생 시 exceptionHandler 메서드가 호출된다.
```
@RestController
@Slf4j
public class ExceptionController {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> exceptionHandler(Exception e) {
        log.info("\n\n=== Exception ===\n* message: " + e.getMessage());
        return ResponseEntity.badRequest().body(CommonResponseDto.createResponseDto(String.valueOf(HttpStatus.INTERNAL_SERVER_ERROR.value()), "시스템 오류"));
    }
    
    @ExceptionHandler(DuplicatedCarNumberException.class)
    public ResponseEntity<?> duplicatedCarNumberHandler(DuplicatedCarNumberException e) {
        log.info("\n\n=== DuplicatedCarNumberException ===\n* message: " + e.getMessage());
        return ResponseEntity.badRequest().body(CommonResponseDto.createResponseDto(String.valueOf(HttpStatus.BAD_REQUEST.value()), e.getMessage()));
    }

}
```

## 결과 확인
```
MockHttpServletResponse:
           Status = 400
    Error message = null
          Headers = [Content-Type:"application/json;charset=UTF-8"]
     Content type = application/json;charset=UTF-8
             Body = {"status":"400","message":"이미 매입되어있는 차량입니다.(차량번호: 04구4716)","data":null,"responseDate":"2021-05-13T17:12:11.1067123"}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```