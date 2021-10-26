## JVM(Java Virtual Machine)
* 자바 가상 머신으로 자바 바이트 코드(.class 파일)를 OS에 특화된 코드로 변환(인터프리터와 JIT 컴파일러)하여 실행합니다.
* 바이트 코드를 실행하는 표준(JVM 자체는 표준)이자 구현체(특정 밴더가 구현한 JVM)입니다.
* JVM 밴더: 오라클, 아마존, Azul, ...
* 특정 플랫폼에 종속적.

![1]()   
(바이트코드 예시)

## JRE(Java Runtime Environment): JVM + 라이브러리
* 자바 애플리케이션을 실행할 수 있도록 구성된 배포판
* JVM과 핵심 라이브러리 및 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일을 가지고 있습니다.
* 개발 관련 도구는 포함하지 않습니다. (그건 JDK에서 제공)

![2]()   
(jre\bin 내부)
* java.exe는 존재하지만 컴파일할때 필요한 javac.exe는 존재하지 않습니다.
* 따라서 java 실행은 가능합니다.
```
java HelloJava
```

## JDK(Java Development Kit): JRE + 개발 툴
* JRE + 개발에 필요한 툴
* 소스 코드를 작성할 때 사용하는 자바 언어는 플랫폼에 독립적.
* 오라클은 자바 11부터는 JDK만 제공하며 JRE를 따로 제공하지 않습니다.
  * 예전에는 JRE, JDK 2가지를 모두 제공했었습니다.
* 개발자라면 JDK를 설치해서 사용하면 됩니다.

## 자바
* 프로그래밍 언어
```
public class HelloJava {
    
    public static void main(String args[]) {
        System.out.println("Hello, Java");
    }
}
```
* JDK에 들어있는 자바 컴파일러(javac)를 사용하여 바이트코드(.class 파일)로 컴파일 할 수 있습니다.
* 오라클에서 만든 Oracle JDK 11 버전부터 상용으로 사용할 때 유료.
  * 오라클에서 만든 Oracle Open JDK는 무료.
  * 오라클에서 만들지 않은 Open JDK는 무료.
  * 사실상 무료

## JVM 언어
* JVM 기반으로 동작하는 프로그래밍 언어
* 클로저, 그루비, JRuby, Jython, Kotlin, Scala, ...
  * 예시로 Kotlin의 경우 컴파일하면 바이트코드(.class 파일)이 생기기 때문에 JVM으로 실행할 수 있습니다.

## 참조
* [더 자바, 코드를 조작하는 다양한 방법]()