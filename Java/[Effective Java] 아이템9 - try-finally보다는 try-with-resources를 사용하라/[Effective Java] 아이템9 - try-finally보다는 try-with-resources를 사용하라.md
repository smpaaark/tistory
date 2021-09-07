자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많습니다.

## try-finally
전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 try-finally가 쓰였습니다.
```
static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    } finally {
        br.close();
    }
}
```
* 더 이상 자원을 회수하는 최선의 방책이 아닙니다.

```
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```
* 자원이 둘 이상이면 try-finally 방식은 너무 지저분합니다.

### 결점
예외는 try 블록과 finally 블록 모두에서 발생할 수 있는데, 예컨대 기기에 물리적인 문제가 생긴다면 firstLineOfFile 메서드 안의 readLine 메서드가 예외를 던지고, 같은 이유로 close 메서드도 실패할 것입니다. 이런 상황이라면 두번째 예외가 첫 번째 예외를 완전히 집어삼켜 버립니다. 그러면 스택 추적 내역에 첫 번째 예외에 관한 정보는 남지 않게 되어, 실제 시스템에서의 디버깅을 몹시 어렵게 합니다(일반적으로 문제를 진단하려면 처음 발생한 예외를 보고 싶을 것입니다).

## try-with-resources
자바 7부터 나왔으며 이 구조를 사용하려면 해당 자원이 AutoCloseable 인터페이스를 구현해야 합니다. 단순히 void를 반환하는 close 메서드 하나만 덩그러니 정의한 인터페이스입니다. 자바 라이브러리와 서드파티 라이브러리들의 수많은 클래스와 인터페이스가 이미 AutoCloseable을 구현하거나 확장해뒀습니다.
```
static String firstLineOfFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(
            new FileReader(path))) {
        return br.readLine();
    }
}
```
* 자원을 회수하는 최선책

```
static void copy(String src, String dst) throws IOException {
    try (InputStream   in = new FileInputStream(src);
            OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }
}
```
* 복수의 자원을 처리하는 try-with-resources

### 장점
짧고 읽기 수월할 뿐 아니라 문제를 진단하기도 훨씬 좋습니다. 실전에서 프로그래머에게 보여줄 예외 하나만 보존되고 여러 개의 다른 예외가 숨겨질 수도 있습니다. 이렇게 숨겨진 예외들도 그냥 버려지지는 않고, 스택 추적 내역에 '숨겨졌다(suppressed)'는 꼬리표를 달고 출력됩니다. 또한, 자바 7에서 Throwable에 추가된 getSuppressed 메서드를 이용하면 프로그램 코드에서 가져올 수도 있습니다.

### catch 절
보통의 try-finally에서처럼 try-with-resources에서도 catch 절을 쓸 수 있습니다. catch 절 덕분에 try 문을 더 중첩하지 않고도 다수의 예외를 처리할 수 있습니다.
```
static String firstLineOfFile(String path, String defaultVal) {
    try (BufferedReader br = new BufferedReader(
            new FileReader(path))) {
        return br.readLine();
    } catch (IOException e) {
        return defaultVal;
    }
}
```
* try-with-resources를 catch 절과 함께 쓰는 모습

## 핵심 정리
> 꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용해야합니다. 예외는 없습니다. 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용합니다. try-finally로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, try-with-resources로는 정확하고 쉽게 자원을 회수할 수 있습니다.

## 참조
* [Effective Java 3판](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966262281)