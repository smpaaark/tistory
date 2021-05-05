## 코드
```
PriorityQueue<NumCount> queue = new PriorityQueue<>((a, b) -> {
    return b.count - a.count;
});
```
뒤의 인자 - 앞의 인자를 리턴하면 MaxHeap이다.

## 결과
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%20%26%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/PriorityQueue%20MaxHeap%2C%20MinHeap%20%EC%84%A0%EC%96%B8%20%EB%B0%A9%EB%B2%95/1.PNG)

## 코드
```
PriorityQueue<NumCount> queue = new PriorityQueue<>((a, b) -> {
    return a.count - b.count;
});
```
앞의 인자 - 뒤의 인자를 리턴하면 MinHeap이다.

## 결과
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%20%26%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/PriorityQueue%20MaxHeap%2C%20MinHeap%20%EC%84%A0%EC%96%B8%20%EB%B0%A9%EB%B2%95/2.PNG)