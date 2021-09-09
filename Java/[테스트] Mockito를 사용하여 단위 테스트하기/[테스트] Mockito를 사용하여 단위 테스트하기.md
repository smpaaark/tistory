저 같은 경우 비즈니스 로직을 테스트 할 때는 통합 테스트를 하지 않고 단위 테스트를 진행합니다. 이 때 Service 클래스는 Repository 계층 또는 외부 API 등을 의존하고 있기 때문에 Mockito를 이용하여 이들을 Mocking 해주어야 합니다.

## Service 코드
```
/**
* 외부채널 추가 연동
*/
@Transactional
public BranchChannelOpenResponseDto openBranchChannel(BranchChannelOpenRequestDto requestDto) {
    // BranchChannelId 생성(복합키)
    BranchChannelId branchChannelId = BranchChannelId.create(requestDto);

    // BranchChannel 조회
    BranchChannel branchChannel = branchChannelRepository.findById(branchChannelId).orElseThrow(()
            -> new NotFoundBranchChannelException("존재하지 않는 매장-채널 데이터입니다. (branchChannelId: " + branchChannelId));

    // BranchPosSettingId 생성(복합키)
    BranchPosSettingId branchPosSettingId = BranchPosSettingId.create(requestDto);
    // BranchPosSetting 조회
    BranchPosSetting branchPosSetting = branchPosSettingRepository.findById(branchPosSettingId).orElseThrow(()
            -> new NotFoundBranchPosSettingException("포스 환경 설정에 데이터가 존재하지 않습니다. (branchPosSettingId: " + branchPosSettingId));

    // 조회한 BranchChannel 연동 처리
    branchChannel.useChannel();

    // 포스 신규주문 조회 주기 30초 설정
    branchPosSetting.updateOrderAlarmInterval();

    return new BranchChannelOpenResponseDto(branchChannel);
}
```
* 위와 같이 openBranchChannel 메소드는 여러 개를 의존하고 있습니다.

## 테스트 코드
```
@ExtendWith(SpringExtension.class)
class BranchServiceTest {

    @InjectMocks
    BranchService branchService;

    @Mock
    BranchChannelRepository branchChannelRepository;

    @Mock
    BranchPosSettingRepository branchPosSettingRepository;

    @Test
    public void 외부채널_추가_연동_성공() throws Exception {
        // given
        String brandCode = "10";
        String branchId = "19998";
        String channelCode = "40";
        BranchChannelOpenRequestDto requestDto = BranchChannelOpenRequestDto.builder()
                .brandCode(brandCode)
                .branchId(branchId)
                .channelCode(channelCode)
                .build();

        BranchChannel branchChannel = BranchChannel.builder()
                .brandCode(brandCode)
                .branchId(branchId)
                .channelCode(channelCode)
                .build();
        given(branchChannelRepository.findById(any())).willReturn(Optional.of(branchChannel));
        given(branchPosSettingRepository.findById(any())).willReturn(Optional.of(BranchPosSetting.builder().build()));

        // when
        BranchChannelOpenResponseDto responseDto = branchService.openBranchChannel(requestDto);

        // then
        assertThat(responseDto.getBrandCode()).isEqualTo(brandCode);
        assertThat(responseDto.getBranchId()).isEqualTo(branchId);
        assertThat(responseDto.getChannelCode()).isEqualTo(channelCode);
        assertThat(responseDto.getUseFlag()).isEqualTo("Y");
    }

    ...

}
```
* \@Mock을 사용하기 위해서는 \@ExtendWith(MockitoExtension.class)를 사용해야 하는데 위 코드처럼 \@ExtendWith(SpringExtension.class)를 사용해도 정상 작동 합니다.
* 테스트할 branchService에 \@InjectMocks을 붙여줍니다.
  * 이 애노테이션을 사용하면 해당 빈을 생성할 때 \@Mock이 붙어있는 Mock 객체들을 함께 주입해줍니다.
* Mocking할 클래스에 \@Mock을 붙여줍니다.
* given(branchChannelRepository.findById(any())).willReturn(Optional.of(branchChannel));
  * given(Mocking할 행위).willReturn(그 행위의 반환 값 설정)

### 결과
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5B%ED%85%8C%EC%8A%A4%ED%8A%B8%5D%20Mockito%EB%A5%BC%20%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC%20%EB%8B%A8%EC%9C%84%20%ED%85%8C%EC%8A%A4%ED%8A%B8%ED%95%98%EA%B8%B0/1.PNG)   

## Exception 발생하는 상황 테스트
```
@Test
public void 외부채널_추가_연동_실패_존재하지_않는_매장_채널() throws Exception {
    // given
    String brandCode = "10";
    String branchId = "19998";
    String channelCode = "40";
    BranchChannelOpenRequestDto requestDto = BranchChannelOpenRequestDto.builder()
            .brandCode(brandCode)
            .branchId(branchId)
            .channelCode(channelCode)
            .build();

    given(branchChannelRepository.findById(any())).willReturn(Optional.empty());

    // when
    // then
    assertThatThrownBy(() -> branchService.openBranchChannel(requestDto))
            .isInstanceOf(NotFoundBranchChannelException.class)
            .hasMessageContaining("존재하지 않는 매장-채널 데이터입니다.");
}
```
* assertThatThrownBy를 사용하여 Exception이 발생하는 상황을 테스트할 수 있습니다.

### 결과
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/%5B%ED%85%8C%EC%8A%A4%ED%8A%B8%5D%20Mockito%EB%A5%BC%20%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC%20%EB%8B%A8%EC%9C%84%20%ED%85%8C%EC%8A%A4%ED%8A%B8%ED%95%98%EA%B8%B0/2.PNG)

## 참조
* [더 자바, 애플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/the-java-application-test)