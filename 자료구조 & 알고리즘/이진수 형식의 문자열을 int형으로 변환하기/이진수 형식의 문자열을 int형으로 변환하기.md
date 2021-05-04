LeetCode 문제를 풀다가 이진수 형식의 문자열을 int형으로 변환해야하는 경우가 생겼다.   
처음에는 반복문으로 계산하려다가 혹시 몰라서 구글링 해봤는데 쉽게 하는 방법이 있었다.   

## 코드
```
public static void main(String[] args) {
    System.out.println(Integer.parseInt("100", 2));
    System.out.println(Integer.parseInt("101", 2));
    System.out.println(Integer.parseInt("110", 2));
    System.out.println(Integer.parseInt("111", 2));
}
```

## 결과
![1]()