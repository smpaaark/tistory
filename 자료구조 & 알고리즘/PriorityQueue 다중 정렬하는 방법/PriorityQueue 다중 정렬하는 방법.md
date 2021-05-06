PriorityQueue를 사용할때 입력되는 정수에 대해 해당하는 Map의 값에 따라 정렬한 후    
Map의 값이 같을 경우엔 입력 값에 따라 정렬하고 싶을 때 아래와 같이 구현하면 된다.

## 코드
```
PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> {
    if (map.get(a) > map.get(b)) {
        return 1;
    } else if (map.get(a) < map.get(b)) { // 여기까지 map의 값에 따라 정렬
        return -1;
    } else { // 같으면 입력 값을 비교
        return a - b;
    }
});
```
위 예시는 오름차순 정렬 코드입니다.
