Hash Table이란 검색하고자 하는 Key값을 입력 받고   
Hash Function을 통해 반환받은 Hash Code를   
배열의 Index로 환산을 해서 데이터에 접근하는 방식의 자료구조이다.   
key값은 문자열, 숫자, 파일 데이터 등등이 될 수 있다.   
Hash Function은 key값이 같다면 크기에 상관 없이 동일한 HashCode를 만들어준다.   

## 장점
Hash Table의 가장 큰 장점은 검색 속도가 매우 빠르다는 것이다. 
### 검색이 빠른 이유  
Hash Function을 통해 얻은 HashCode는 정수이다.   
그래서 배열 공간을 고정된 크기만큼 미리 만들어 놓고    
HashCode를 배열의 갯수로 나머지 연산을 해서 배열에 나눠 담는 것이다.   
즉 HashCode 자체가 배열의 index로 사용되기 때문에 검색할 필요없고 바로 데이터에 접근이 가능하다.   

## 단점
Hash Algorithm을 잘못 만들면 한 인덱스에 여러개의 데이터가 쌓이게 되어 Collison(충돌)이 발생한다.    
Collison이 많은 Hash Algorithm의 경우 검색 시간이 O(n)이 걸릴 수 있다.   
따라서 입력받은 key값을 통해 얼마나 잘 골고루 분배하느냐에 따라 좋은 알고리즘인지가 결정된다.   

다른 key값이지만 같은 HashCode로 반환되거나,   
다른 HashCode지만 같은 index로 배정받을 수 있는데 이들 모두 Collison이라고 한다.   

따라서 Collison을 최소화하기 위해 좋은 Hash Algorithm을 만드는 일은 Hash Table에서 매우 중요한 이슈이다.   

## 참고
* [[자료구조 알고리즘] 해쉬테이블(Hash Table)에 대해 알아보고 구현하기](https://youtu.be/Vi0hauJemxA)