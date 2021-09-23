* Transfer-Encoding
* Range, Content-Range

## 전송 방식 설명
* 단순 전송
  * Content-Length
  * 한번에 요청하고 한번에 받습니다.
  * Content-Lengh를 알고 있을 때 사용
* 압축 전송
  * Content-Encoding
  * gzip 같은거로 압축해서 전송
* 분할 전송
  * Transfer-Encoding
  * chunked: 덩어리로 쪼개서 보냅니다.
  * 일정 바이트씩 데이터를 전송
  * Content-Length를 보내면 안됩니다.
  ```
  Transfer-Encoding: chunked

  5
  Hello
  5
  World
  5
  \r\n
  ```
* 범위 전송
  * Range, Content-Range
  * 어느 정도 까지 받았다가 끊겨서 다시 요청할 때 범위를 지정해서 요청
  ```
  Range: bytes=1001-2000
  ```
  ```
  Content-Range: bytes 1001-2000 / 2000
  ```

## 참조
* [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)