## Array
가장 기본적인 자료구조인 Array 자료구조는, 논리적 저장 순서와 물리적 저장 순서가 일치한다.    
따라서 인덱스(index)로 해당 원소(element)에 접근할 수 있다.   
그렇기 때문에 찾고자 하는 원소의 인덱스 값을 알고 있으면 시간복잡도 O(1)에 해당 원소로 접근할 수 있다.   

하지만 삭제 또는 삽입의 과정에서는 해당 원소에 접근하여 작업을 완료한 뒤(O(1)), 또 한 가지의 작업을 추가적으로 해줘야 하기 때문에, 시간이 더 걸린다.    
만약 배열의 원소 중 어느 원소를 삭제했다고 했을 때, 배열의 연속적인 특징이 깨지게 된다.   



## 참고
* [한재엽님 GitHub Array vs Linked List](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/DataStructure#array-vs-linked-list)