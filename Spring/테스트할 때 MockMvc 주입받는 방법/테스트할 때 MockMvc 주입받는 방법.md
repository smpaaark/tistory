JUnit으로 테스트 코드를 작성할 때 MockMvc를 많이 사용하게 된다.   
MockMvc를 주입받는 방법은 2가지가 있다.

## @WebMvcTest 사용
@WebMvcTest를 사용하면 컨트롤러 클래스를 주입받을 수 있다.   
단, 서비스나 리포지토리 클래스는 주입받을 수 없다.   
따라서 컨트롤러만 테스트할 경우 사용한다.   
이 때 @WebMvcTest를 사용하면 MockMvc를 주입받아서 사용할 수 있다.

### 코드
```
@RunWith(SpringRunner.class)
@WebMvcTest
public class HelloControllerTest {

    @Autowired
    private MockMvc mvc;

    @Test
    public void hello가_리턴된다() throws Exception {
        // given
        String hello = "hello";

        // when
        mvc.perform(get("/hello"))
                // then
                .andExpect(status().isOk())
                .andExpect(content().string(hello));
    }
}
```

## @SpringBootTest 사용
@SpringBootTest를 사용하면 컨트롤러, 서비스, 리포지토리 모두 사용 가능하다.   
단, MockMvc를 주입받으려면 @AutoConfigureMockMvc를 같이 선언해야 한다.

### 코드
```
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
@Transactional
public class CarApiControllerTest {

    @Autowired
    MockMvc mvc;

    @Autowired
    ObjectMapper objectMapper;

    @Test
    public void 차량_매입하기() throws Exception {
        String carNumber = "04구4716";
        String vin = "12345678";
        Category category = Category.DOMESTIC;
        String model = "더 뉴 K5";
        String color = "검정";
        String productionYear = "2018";
        CarSaveRequestDto requestDto = CarSaveRequestDto.builder()
                .carNumber(carNumber)
                .vin(vin)
                .category(category)
                .model(model)
                .color(color)
                .productionYear(productionYear)
                .build();

        MvcResult result = mvc.perform(post("/api/car")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(objectMapper.writeValueAsString(requestDto)))
                .andDo(print())
                .andExpect(status().isOk())
                .andReturn();
    }
}
```