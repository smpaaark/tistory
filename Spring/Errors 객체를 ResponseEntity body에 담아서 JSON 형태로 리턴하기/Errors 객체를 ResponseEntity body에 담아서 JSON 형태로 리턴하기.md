스프링의 \@Valid를 사용하면 유효성 검증에 실패한 내용이 Errors에 담긴다.   
```
@PostMapping("api/car")
public ResponseEntity<?> saveCar(@RequestBody @Valid CarSaveRequestDto requestDto, Errors errors) {
    log.info("\n\n=== saveCar start ===\n* requestDto: " + requestDto + "\n");
    if (errors.hasErrors()) {
        return ResponseEntity.badRequest().body(errors);
    }

    CarSaveResponseDto responseDto = carService.save(requestDto);

    log.info("\n\n=== saveCar end ===\n* savedCarId: " + responseDto.getId() + "\n");
    return ResponseEntity.created(null).body(responseDto);
}
```

하지만 이 Errors 객체를 ResponseEntity의 body에 담아서 리턴하면 에러가 발생한다.   
```
org.springframework.web.util.NestedServletException: Request processing failed; nested exception is org.springframework.http.converter.HttpMessageConversionException: Type definition error: [simple type, class org.springframework.validation.DefaultMessageCodesResolver]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: No serializer found for class org.springframework.validation.DefaultMessageCodesResolver and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) (through reference chain: org.springframework.validation.BeanPropertyBindingResult["messageCodesResolver"])

	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014)
	at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:652)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)
    ...
```
Errors 객체는 JSON으로 바로 변환할 수 없기 때문에 에러가 발생한다.   
그 이유는 Errors 객체는 자바 빈 스펙을 준수하고 있는 객체가 아니기 때문이다.   

따라서 JsonSerializer\<Errors>를 상속받는 별도의 클래스를 만들고 serialize 메서드를 Override 해야한다.   
이때 \@JsonComponent를 꼭 붙여줘야 한다.   
### 코드
```
@JsonComponent
public class ErrorsSerializer extends JsonSerializer<Errors> {

	@Override
	public void serialize(Errors errors, JsonGenerator generator, SerializerProvider provider) throws IOException {
		generator.writeStartArray();
		errors.getFieldErrors().stream().forEach(e -> {
			try {
				generator.writeStartObject();
				generator.writeStringField("field", e.getField());
				generator.writeStringField("objectName", e.getObjectName());
				generator.writeStringField("code", e.getCode());
				generator.writeStringField("defaultMessage", e.getDefaultMessage());
				Object rejectedValue = e.getRejectedValue();
				if (rejectedValue != null) {
					generator.writeStringField("rejectedValue", e.getRejectedValue().toString());
				}
				generator.writeEndObject();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
		});

		errors.getGlobalErrors().stream().forEach(e -> {
			try {
				generator.writeStartObject();
				generator.writeStringField("objectName", e.getObjectName());
				generator.writeStringField("code", e.getCode());
				generator.writeStringField("defaultMessage", e.getDefaultMessage());
				generator.writeEndObject();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
		});
		generator.writeEndArray();
	}

}
```

### 결과 로그
```
MockHttpServletResponse:
           Status = 400
    Error message = null
          Headers = [Content-Type:"application/json"]
     Content type = application/json
             Body = [{"field":"model","objectName":"carSaveRequestDto","code":"NotEmpty","defaultMessage":"must not be empty"},{"field":"carNumber","objectName":"carSaveRequestDto","code":"NotEmpty","defaultMessage":"must not be empty"},{"field":"vin","objectName":"carSaveRequestDto","code":"NotEmpty","defaultMessage":"must not be empty"},{"field":"category","objectName":"carSaveRequestDto","code":"NotNull","defaultMessage":"must not be null"},{"field":"color","objectName":"carSaveRequestDto","code":"NotEmpty","defaultMessage":"must not be empty"},{"field":"productionYear","objectName":"carSaveRequestDto","code":"NotEmpty","defaultMessage":"must not be empty"}]
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```