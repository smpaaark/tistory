Junit으로 테스트 코드를 작성하다보면 private 메서드를 테스트해야하는 경우가 있습니다. 이 경우 Java Reflection을 활용하여 private 메서드 및 필드를 사용할 수 있습니다.

## private 메서드
```
@ToString
@NoArgsConstructor
public class BranchChannelId implements Serializable {

    ...

    private static int getChannelSeq(String channelCode) {
        switch (channelCode) {
            case "10":
                return 0;
            case "20":
                return 1;
            case "30":
                return 2;
            case "40":
                return 3;
            case "00":
                return 4;
            case "70":
                return 5;
            default:
                return -1;
        }
    }

}
```
* channelCode 값에 따라 특정 int 값을 반환하는 private 메서드입니다.

## 실패하는 테스트 코드
![1]()   
* private 메서드에 접근할 수 없다는 메시지와 함께 컴파일 실패합니다.

## 성공하는 테스트 코드
```
@Test
public void getChannelSeq() throws Exception {
    // given
    String channelCode = "40";
    Method getChannelSeqMethod = BranchChannelId.class.getDeclaredMethod("getChannelSeq", String.class);
    getChannelSeqMethod.setAccessible(true);

    // when
    int result = (int) getChannelSeqMethod.invoke(BranchChannelId.class, channelCode);

    // then
    assertThat(result).isEqualTo(3);
}
```
* getDeclaredMethod(메소드명, 파라미터의 타입.class, ...)
  * private 메소드를 가져옵니다.
* setAccessible(true)
  * 해당 메서드에 접근을 허용합니다.
* invoke(클래스, 실제파라미터, ...)
  * 메서드를 실행시킵니다.

### 결과
![2]()

## 참조
* [[JUnit] private 메서드, 변수 테스트 방법 - Crocus](https://www.crocus.co.kr/1665)