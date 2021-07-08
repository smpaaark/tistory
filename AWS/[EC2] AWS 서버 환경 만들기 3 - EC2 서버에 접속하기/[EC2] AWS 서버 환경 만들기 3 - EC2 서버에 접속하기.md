생성한 EC2에 접속을 해보겠습니다.   
* Mac & Linux는 터미널
* 윈도우는 putty   
저는 윈도우를 사용중이기 때문에 putty를 사용하겠습니다.   

>EC2에 오랜 시간 접속이 안되거나, 권한이 없어서 안 된다고 메시지가 나온다면 다음과 같이 확인해 보는 것이 좋습니다.   
>* HostName 값이 정확히 탄력적 IP로 되어있는지 확인
>* EC2 인스턴스가 running 상태인지 확인
>* EC2 인스턴스의 보안그룹 -> 인바운드 규칙에서 현재 본인의 IP가 등록되어 있는지 확인

윈도우에서는 Mac과 같이 ssh 접속하기엔 불편한 점이 많아 별도의 클라이언트 putty를 설치하겠습니다.   
[putty 사이트](https://www.putty.org/)에 접속하여 실행 파일을 내려받습니다.   
![1]()

실행 파일은 2가지 입니다.
* putty.exe
* puttygen.exe

두 파일을 모두 내려받은 뒤, puttygen.exe 파일을 실행합니다.   
![2]()

putty는 pem 키로 사용이 안 되며 pem 키를 ppk 파일로 변환을 해야만 합니다.   
puttygen은 이 과정을 진행해 주는 클라이언트입니다.   
puttygen 화면에서 상단 [Conversions => Import Key]를 선택해서 내려받은 pem 키를 선택합니다.   
![3]()   
![4]()

그럼 다음과 같이 자동으로 변환이 진행됩니다.   
[Save private key] 버튼을 클릭하여 ppk 파일을 생성합니다.   
경고 메시지가 뜨면 [예]를 클릭하고 넘어갑니다.   
![5]()   
![6]()

ppk 파일이 저장될 위치와 ppk 이름을 등록합니다.   
![7]()

ppk 파일이 잘 생성되었으면 putty.exe 파일을 실행하여 다음과 같이 각 항목을 등록합니다.   
![8]()
* HostName
  * username@public_Ip를 등록합니다.   
  * 우리가 생성한 Amazon Linux는 ec2-user가 username이라서 ec2-user@탄력적 IP 주소를 등록하면 됩니다.
* Port
  * ssh 접속 포트인 22를 등록합니다.
* Connection type
  * SSH를 선택합니다.

항목들을 모두 채웠다면 왼쪽 사이드바에 있는 [Connection => SSH => Auth]를 차례로 클릭해서 ppk 파일을 로드할 수 있는 화면으로 이동합니다.   
[Browse...] 버튼을 클릭합니다.   
![9]()

좀 전에 생성한 ppk 파일을 선택해서 불러옵니다.   
정상적으로 불러왔다면 다시 [Session] 탭으로 이동하여 [Saved Sessions]에 현재 설정들을 저장할 이름을 등록하고 [Save] 버튼을 클릭합니다.   
![10]()   
![11]()   

저장한 뒤 [open] 버튼을 클릭하여 다음과 같이 SSH 접속 알림이 등장합니다.   
[Accept]를 클릭합니다.   
![12]()

그럼 다음과 같이 SSH 접속이 성공한 것을 확인할 수 있습니다.   
![13]()   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)