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
HashCode를 배열의 갯수로 나머지연산을 해서 배열에 나눠 담는 것이다.   
그 말은 HashCode 자체가 배열의 index로 사용되기 때문에 바로 데이터에 접근이 가능하다.   

## 단점



## 참고
* [[자료구조 알고리즘] 해쉬테이블(Hash Table)에 대해 알아보고 구현하기](https://youtu.be/Vi0hauJemxA)