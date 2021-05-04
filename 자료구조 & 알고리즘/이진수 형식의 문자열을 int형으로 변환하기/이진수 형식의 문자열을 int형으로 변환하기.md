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
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%20%26%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/%EC%9D%B4%EC%A7%84%EC%88%98%20%ED%98%95%EC%8B%9D%EC%9D%98%20%EB%AC%B8%EC%9E%90%EC%97%B4%EC%9D%84%20int%ED%98%95%EC%9C%BC%EB%A1%9C%20%EB%B3%80%ED%99%98%ED%95%98%EA%B8%B0/1.PNG)