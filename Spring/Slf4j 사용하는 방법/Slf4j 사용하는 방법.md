롬복이 적용되어 있다는 가정 하에 \@Slf4j만 선언해주면 ```log``` 변수를 통해 Slf4j 로그를 사용할 수 있다.   

## 코드
```
@RestController
@RequiredArgsConstructor
@Slf4j
public class CarApiController {

    private final CarService carService;

    @PostMapping("api/car")
    public String saveCar(@RequestBody CarSaveRequestDto requestDto, BindingResult result) {
        log.info("\n=== saveCar start ===\n=== requestDto: " + requestDto);
        if (result.hasErrors()) {
            return result.toString();
        }

        Long savedCarId = carService.save(requestDto);

        log.info("\n=== saveCar end ===\n=== savedCarId: " + savedCarId);
        return String.valueOf(savedCarId);
    }

}
```

## 로그 확인
```
2021-05-12 13:51:37.741  INFO 46800 --- [           main] com.usedcar.admin.web.CarApiController   : 
=== saveCar start ===
=== requestDto: CarSaveRequestDto(carNumber=04구4716, vin=12345678, category=DOMESTIC, model=더 뉴 K5, color=검정, productionYear=2018)
.
.
.
2021-05-12 13:51:37.975  INFO 46800 --- [           main] com.usedcar.admin.web.CarApiController   : 
=== saveCar end ===
=== savedCarId: 1
```