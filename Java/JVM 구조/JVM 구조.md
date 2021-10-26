![5](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/JVM%20%EA%B5%AC%EC%A1%B0/5.jpg)

## 클래스 로더 시스템
* .class에서 바이트코드를 읽고 메모리에 저장
* 로딩: 클래스 읽어오는 과정
* 링크: 레퍼런스를 연결하는 과정
* 초기화: static 값들 초기화 및 변수에 할당
```
public class App {

	static String myName;

	static {
		myName = "seongmin";
	}

  ...
}
```
(static 변수 선언)

```
public class Smpaaark {

    public void work() {
        System.out.println(App.myName);
    }
}
```
(위에 선언한 static 변수를 다른 클래스에서 사용)
* 클래스명.static변수명 형태로 사용합니다.

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/JVM%20%EA%B5%AC%EC%A1%B0/1.PNG)   
(인텔리J를 통해 본 class 파일)
* 원래는 바이트코드 형태이지만 IDE에서 우리가 이해할 수 있을 수준으로 디컴파일해서 보여줍니다.

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/JVM%20%EA%B5%AC%EC%A1%B0/2.PNG)
(실제 바이트코드)

## 메모리
* 메소드 영역에는 클래스 수준의 정보 (클래스 이름, 부모 클래스 이름, 메소드, 변수) 저장. 공유 자원입니다.
  * 클래스 이름은 풀 패키지 경로로 저장합니다.
  * 모든 클래스는 상속받은 클래스가 있습니다. (= Object)
```
public class App {

	...

	public static void main(String[] args) {
		System.out.println(App.class.getSuperclass());
	}
}
```
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/JVM%20%EA%B5%AC%EC%A1%B0/3.PNG)   
(App 클래스는 코드상으로 아무 클래스도 상속받지 않았지만 상위 클래스를 출력하면 Object 클래스가 출력됩니다.)

```
public class App {

	...

	private String foo = "bar";

	...
}
```
(이런 변수 정보들도 저장됩니다.)
* 힙 영역에는 객체를 저장. 공유 자원입니다.
* 스택 영역에는 쓰레드마다 런타임 스택을 만들고, 그 안에 메소드 호출을 스택 프레임이라 부르는 블럭으로 쌓습니다. 쓰레드 종료하면 런타임 스택도 사라집니다.
  * 콘솔에 찍히는 에러 메시지에 메서드가 쌓여있는데 이게 메서드 호출 스택이 쌓여있는 것입니다.

![4](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/JVM%20%EA%B5%AC%EC%A1%B0/4.PNG)
(콘솔 에러 메시지)

* PC(Program Counter) 레지스터: 쓰레드 마다 쓰레드 내 현재 실행할 instruction의 위치를 가리키는 포인터가 생성됩니다.
* 네이티브 메소드 스택

## 실행 엔진
* 인터프리터: 바이트코드를 한줄 씩 실행.
* JIT 컴파일러: 인터프리터 효율을 높이기 위해, 인터프리터가 반복되는 코드를 발견하면 JIT 컴파일러로 반복되는 코드를 모두 네이티브 코드로 바꿔둡니다. 그 다음부터 인터프리터는 네이티브 코드로 컴파일된 코드를 바로 사용합니다.
  * 프로그램 실행 속도를 향상시킵니다.
* GC(Garbage Collector): 더이상 참조되지 않는 객체를 모아서 정리합니다.

## JNI(Java Native Interface)
* 자바 애플리케이션에서 C, C++, 어셈블리로 작성된 함수를 사용할 수 있는 방법 제공
* Native 키워드를 사용한 메소드 호출
```
@HotSpotIntrinsicCandidate
public static native Thread currentThread();
```
(Thread 클래스)

## 네이티브 메소드 라이브러리
* C, C++로 작성 된 라이브러리

## 참조
* [더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)