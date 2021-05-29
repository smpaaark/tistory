## Transport Layer
End Point간 ```신뢰성```있는 데이터 ```전송```을 담당하는 계층
* 신뢰성: 데이터를 순차적, 안정적으로 전달
* 전송: 포트 번호에(해당하는 프로세스) 데이터를 전달

## 전송계층(Transport Layer)의 중요성
* 순차 전송
* 흐름 제어(Flow Control): 송수신자 간의 데이터 처리 속도 관련
* 혼잡 제어(Congestion Control): 네트워크의 데이터 처리 속도 관련
* 오류 감지(Error Detection)

## TCP (Transmission Control Protocol)
* 신뢰성있는 데이터 통신을 가능하게 해주는 프로토콜
* 특징: Connection 연결 ([3 way-handshake](https://smpark1020.tistory.com/184)) - 양방향 통신
* 데이터의 순차 전송을 보장
* Flow Control(흐름 제어)
* Congestion Control(혼잡 제어)
* Error Detection(오류 감지)

### TCP의 데이터 전송 방식
1. Client가 패킷 송신
2. Server에서 ACK 송신
3. ACK를 수신하지 못하면 재전송

### 4 way-handshake (Connection close)
1. 데이터를 전부 송신한 Client가 FIN 송신
2. Server가 ACK 송신
3. Server에서 남은 패킷 송신 (일정 시간 대기)
4. Server가 FIN 송신
5. Client가 ACK 송신

### TCP의 문제점
* 매번 Connection을 연결해서 시간 손실 발생
* 패킷을 조금만 손실해도 재전송

## UDP (User Datagram Protocol)
* TCP보다 신뢰성이 떨어지지만 전송 속도가 일반적으로 빠른 프로토콜
* 순차 전송 X, 흐름 제어 X, 혼잡 제어 X
* Connectionless (3 way-handshake X)
* Error Detection
* 비교적 데이터의 신뢰성이 중요하지 않을 때 사용 (ex. 영상 스트리밍)

### UDP의 데이터 전송 방식
1. Client가 패킷 송신
2. 끝

## 참고
* [[10분 테코톡] 👨‍🏫르윈의 TCP UDP](https://youtu.be/ikDVGYp5dhg)