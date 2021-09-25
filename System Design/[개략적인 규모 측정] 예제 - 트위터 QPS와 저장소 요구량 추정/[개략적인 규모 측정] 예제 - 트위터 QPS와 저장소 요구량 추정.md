## 가정
* 월간 능동 사용자(monthly active user)는 3억(300million) 명입니다.
* 50%의 사용자가 트위터를 매일 사용합니다.
* 평균적으로 각 사용자는 매일 2건의 트윗을 올립니다.
* 미디어를 포함하는 트윗은 10% 정도입니다.
* 데이터는 5년간 보관됩니다.

## 추정
QPS(Query Per Second) 추정치
* 일간 능동 사용자(Daily Active User, DAU) = 3억 X 50% = 1.5억(150million)
* QPS = 1.5억 X 2트윗/24시간/3600초 = 약 3500
* 최대 QPS(Peek QPS) = 2 X QPS = 약 7000

## 미디어 저장을 위한 저장소 요구량
* 평균 트윗 크기
  * tweet_id에 64바이트
  * 텍스트에 140바이트
  * 미디어에 1MB
* 미디어 저장소 요구량: 1.5억 X 2 X 10% X 1MB = 30TB/일
* 5년간 미디어를 보관하기 위한 저장소 요구량: 30TB X 365 X 5 = 약 55PB

## 참조
* [가상 면접 사례로 배우는 대규모 시스템 설계 기초](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966263158&orderClick=&Kc=)