오직 1개의 원소만 1번 들어있고 나머지 원소는 모두 2번씩 들어있는 int형 배열이 주어진다.   
여기서 오직 1번만 들어있는 원소를 찾을때 XOR을 이용하면 시간 복잡도 O(n), 공간 복잡도 O(1)로 해결할 수 있다.   

## 원리
XOR의 핵심은 자기자신과 연산하면 0이되고, 0과 연산하면 자기자신이 된다는 것이다.   
또한, 여러개의 값을 연산할때 순서를 바꿔도 결과는 같다.   
예시로 ```nums = {4, 1, 2, 1, 2}```가 주어진다.   
위 원리를 적용하면 아래와 같다.   
```
4 ^ 1 ^ 2 ^ 1 ^ 2
= (1 ^ 1) ^ (2 ^ 2) ^ 4
= 0 ^ 0 ^ 4
= 0 ^ 4
= 4
```

## 코드
```
// 1 <= nums.length
public int singleNumber(int[] nums) {
    int result = nums[0];
    for (int i = 1; i < nums.length; i++) {
        result ^= nums[i];
    }

    return result;
}
```

## 결과
![1]()

## 참고
* [136. Single Number](https://leetcode.com/problems/single-number/)