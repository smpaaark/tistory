RDS를 처음 생성하면 몇 가지 설정을 필수로 해야합니다.   
우선 다음 3개의 설정을 차례로 진행해 보겠습니다.   
* 타임존
* Character Set
* Max Connection

왼쪽 카테고리에서 [파라미터 그룹] 탭을 클릭해서 이동합니다.   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/1.PNG)   

화면 오른쪽 위의 [파라미터 그룹 생성] 버튼을 클릭합니다.   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/2.PNG)   

세부 정보 위쪽에 DB 엔진을 선택하는 항목이 있습니다.   
여기서 방금 생성한 MariaDB와 같은 버전을 맞춰야 합니다.   
앞에서 10.4.13 버전으로 생성했기 때문에 같은 버전대인 10.4를 선택해야 합니다.   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/3.PNG)   

생성이 완료되면 파라미터 그룹 목록 창에 새로 생성된 그룹을 볼 수 있습니다.   
해당 파라미터 그룹을 클릭합니다.   
![4](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/4.PNG)   

클릭해서 이동한 상세 페이지의 오른쪽을 보면 [수정] 버튼이 있습니다.   
해당 버튼을 클릭해 편집 모드로 전환합니다.   
![5](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/5.PNG)   

편집 모드로 되었다면 이제 하나씩 설정값들을 변경해 보겠습니다.   
먼저 time_zone을 검색하여 다음과 같이 Asia/Seoul을 입력합니다.   
![6](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/6.PNG)   

다음으로 Character Set을 변경합니다.   
Character Set은 항목이 많습니다.   
아래 8개 항목 중 character 항목들은 모두 utf8mb4로, collation 항목들은 utf8mb4_general_ci로 변경합니다.   
utf8과 utf8mb4의 차이는 이모지 저장 가능 여부입니다.   
* character_set_client
* character_set_connection
* character_set_database
* character_set_filesystem
* character_set_results
* character_set_server
* collation_connection
* collation_server

![7](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/7.PNG)

utf8은 이모지를 저장할 수 없지만, utf8mb4는 이모지를 저장할 수 있으므로 보편적으로 utf8mb4를 많이 사용합니다.   

마지막으로 Max Connection을 수정합니다.   
RDS의 Max Connection은 인스턴스 사양에 따라 자동으로 정해집니다.   
현재 프리티어 사양으로는 약 60개의 커넥션만 가능해서 좀 더 넉넉한 값으로 지정합니다.   
![8](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/8.PNG)   

이후에 RDS 사양을 높이게 된다면 기본값으로 다시 돌려놓으면 됩니다.   
설정이 다 되었다면 [계속] 버튼을 클릭하고 변경 사항 검토 완료 후 [변경 사항 적용] 버튼을 클릭해 최종 저장합니다.

이렇게 해서 생성된 파라미터 그룹을 데이터베이스에 연결하겠습니다.   
![9](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/9.PNG)   

옵션 항목에서 DB 파라미터 그룹은 default로 되어있습니다.   
DB 파라미터 그룹을 방금 생성한 신규 파라미터 그룹으로 변경합니다.   
![10](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/10.PNG)

[계속]을 누르면 다음과 같이 수정 사항이 요약된 것을 볼 수 있습니다.   
여기서 반영 시점을 [즉시 적용]으로 합니다.   
![11](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/11.PNG)   

예약된 다음 유지 시간으로 하면 지금 하지 않고, 새벽 시간대에 진행하게 됩니다.   
이 수정사항이 반영되는 동안 데이터베이스가 작동하지 않을 수 있으므로 예약 시간을 걸어두라는 의미지만, 지금은 서비스가 오픈되지 않았기 때문에 즉시 적용합니다.   

간혹 파라미터 그룹이 제대로 반영되지 않을 때가 있습니다.   
정상 적용을 위해 한 번 더 재부팅을 진행합니다.   
![12](https://raw.githubusercontent.com/smpark1020/tistory/master/AWS/%5BRDS%5D%20AWS%EC%97%90%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%ED%99%98%EA%B2%BD%20%EB%A7%8C%EB%93%A4%EA%B8%B0%202%20-%20RDS%20%EC%9A%B4%EC%98%81%ED%99%98%EA%B2%BD%EC%97%90%20%EB%A7%9E%EB%8A%94%20%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/12.PNG)   

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)