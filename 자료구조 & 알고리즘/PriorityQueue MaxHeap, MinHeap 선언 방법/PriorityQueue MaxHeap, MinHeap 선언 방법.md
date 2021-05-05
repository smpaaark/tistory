```
PriorityQueue<NumCount> queue = new PriorityQueue<>((a, b) -> {
    return b.count - a.count;
});
```
뒤의 인자 - 앞의 인자를 리턴하면 MaxHeap이다.

```
PriorityQueue<NumCount> queue = new PriorityQueue<>((a, b) -> {
    return a.count - b.count;
});
```
앞의 인자 - 뒤의 인자를 리턴하면 MinHeap이다.