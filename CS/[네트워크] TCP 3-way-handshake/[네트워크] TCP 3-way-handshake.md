TCP를 이용한 데이터 통신을 할 때 프로세스와 프로세스를 연결하기 위해 가장 먼저 수행되는 과정을 3-way-handshake라고 부른다.   
이 과정이 이루어지고 난 다음에 데이터를 주고받을 수 있다.   

3-way-handshake는 총 3가지 단계로 이루어 진다.   
1. 클라이언트가 서버에게 요청 패킷을 보낸다.   
* TCP 정보에 SYN 플래그, SYN 번호(랜덤한 숫자 a), ACK 번호(0)가 세팅된다.
* TCP, IPv4, 이더넷 정보를 Encapsulation해서 보낸다.
2. 서버가 클라이언트의 요청을 받아들이는 패킷을 보낸다. 
* 클라이언트로부터 받은 정보를 Decapsulation해서 내용을 확인한다.
* 플래그 값을 확인하여 연결 요청을 하는 거구나 인식을 한다.
* SYN + ACK 플래그, SYN 번호(랜덤한 숫자 b), ACK 번호(받은 SYN 번호 + 1 = a + 1)를 세팅한다.
* TCP, IPv4, 이더넷 정보를 Encapsulation해서 보낸다.
3. 클라이언트는 이를 최종적으로 수락하는 패킷을 보낸다.   
* 서버로부터 받은 정보를 Decapsulation해서 내용을 확인한다.
* ACK 플래그, SYN 번호(받은 ACK 번호), ACK 번호(받은 SYN 번호 + 1 = b + 1)를 세팅한다.
  * SYN 번호를 처음 보낼때는 랜덤한 숫자로 보내지만 동기화가 된 다음부터는 받은 ACK 번호로 보낸다.
* TCP, IPv4, 이더넷 정보를 Encapsulation해서 보낸다.

## 참고
* [[따라學IT] 09. 연결지향형 TCP 프로토콜 - TCP 3Way Handshake](https://youtu.be/Ah4-MWISel8)