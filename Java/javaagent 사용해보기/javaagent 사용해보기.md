이전 글에서 Byte Buddy를 사용하여 바이트코드를 조작해봤습니다. 하지만 1가지 문제점이 있었습니다.
```
import net.bytebuddy.ByteBuddy;
import net.bytebuddy.implementation.FixedValue;

import java.io.File;
import java.io.IOException;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class Masulsa {

    public static void main(String[] args) {
        try {
            new ByteBuddy().redefine(Moja.class)
                    .method(named("pullOut")).intercept(FixedValue.value("Rabbit!"))
                    .make().saveIn(new File("C:\\workspace_sts\\classloadersample\\target\\classes\\"));
        } catch (IOException e) {
            e.printStackTrace();
        }

        System.out.println(new Moja().pullOut());
    }
}
```

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/1.PNG)
* 다음과 같이 바이트코드 조작 코드와 출력 코드를 같이 사용하게 되면 "Rabbit!"이 출력되지 않습니다.
* 이미 바이트코드 조작 코드가 실행되기 전에 Moja 클래스를 읽었기 때문입니다.

## 문자열에서부터 참조하도록 코드 변경
```
import net.bytebuddy.ByteBuddy;
import net.bytebuddy.dynamic.ClassFileLocator;
import net.bytebuddy.implementation.FixedValue;
import net.bytebuddy.pool.TypePool;

import java.io.File;
import java.io.IOException;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class Masulsa {

    public static void main(String[] args) {
        ClassLoader classLoader = Masulsa.class.getClassLoader();
        TypePool typePool = TypePool.Default.of(classLoader);
        try {
            new ByteBuddy().redefine(
                        typePool.describe("me.smpaaark.Moja").resolve(),
                        ClassFileLocator.ForClassLoader.of(classLoader))
                    .method(named("pullOut")).intercept(FixedValue.value("Rabbit!"))
                    .make().saveIn(new File("C:\\workspace_sts\\classloadersample\\target\\classes\\"));
        } catch (IOException e) {
            e.printStackTrace();
        }

        System.out.println(new Moja().pullOut());
    }
}
```
* 출력 코드 전까지 Moja 클래스를 직접 읽지 않아도 됩니다.
* 그러므로 class 파일이 변경 된 후 Moja 클래스를 읽어옵니다.

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/2.PNG)
* 따라서 한번에 "Rabbit!"이 출력됩니다.
* 하지만 다른 코드에서 Moja 클래스를 먼저 읽을 경우 위 코드가 의도대로 작동하지 않을 수 있습니다.

## Javaagent JAR 파일 만들기
붙이는 방식은 시작시 붙이는 방식 premain과 런타임 중에 동적으로 붙이는 방식 agentmain이 있습니다. 매개변수로 Instrumentaton을 사용합니다.
* premain
  * 애플리케이션을 실행할 때 옵션으로 줘서 agent를 붙이는 것
  * 아래 예제에서는 premain 방식을 사용합니다.
* agentmain
  * 이미 애플리케이션이 실행되고 있는데 거기에 agent를 붙이는 것

```
public class Masulsa {

    public static void main(String[] args) {
        System.out.println(new Moja().pullOut());
    }
}
```
이제부터 위 코드만 실행해도 "Rabbit!"이 출력되도록 해보겠습니다.

### 새 프로젝트 생성
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/3.PNG)
* 별도의 메이븐 프로젝트를 하나 생성합니다.

### 의존성 추가
```
<dependency>
    <groupId>net.bytebuddy</groupId>
    <artifactId>byte-buddy</artifactId>
    <version>1.11.21</version>
</dependency>
```
* 이전에 추가했던 Byte Buddy 의존성을 pom.xml에 추가합니다.

### MasulsaAgent 클래스 생성
```
import net.bytebuddy.agent.builder.AgentBuilder;
import net.bytebuddy.description.type.TypeDescription;
import net.bytebuddy.dynamic.DynamicType;
import net.bytebuddy.implementation.FixedValue;
import net.bytebuddy.matcher.ElementMatchers;
import net.bytebuddy.utility.JavaModule;

import java.lang.instrument.Instrumentation;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class MasulsaAgent {

    public static void premain(String agentArgs, Instrumentation inst) {
        new AgentBuilder.Default()
                .type(ElementMatchers.any())
                .transform(new AgentBuilder.Transformer() {
                    @Override
                    public DynamicType.Builder<?> transform(DynamicType.Builder<?> builder, TypeDescription typeDescription, ClassLoader classLoader, JavaModule javaModule) {
                        return builder.method(named("pullOut")).intercept(FixedValue.value("Rabbit!"));
                    }
                }).installOn(inst);
    }
}
```
* 위 코드를 추가합니다.
* transform에서 작업한 것들을 installOn(inst)에 적용을 하겠다는 의미입니다.
* return builder.method(named("pullOut")).intercept(FixedValue.value("Rabbit!"));
  * 이전 글에서 했던 작업과 같습니다.

```
import net.bytebuddy.agent.builder.AgentBuilder;
import net.bytebuddy.implementation.FixedValue;
import net.bytebuddy.matcher.ElementMatchers;

import java.lang.instrument.Instrumentation;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class MasulsaAgent {

    public static void premain(String agentArgs, Instrumentation inst) {
        new AgentBuilder.Default()
                .type(ElementMatchers.any())
                .transform((builder, typeDescription, classLoader, javaModule) -> builder.method(named("pullOut")).intercept(FixedValue.value("Rabbit!"))).installOn(inst);
    }
}
```
* 람다를 사용하면 위와 같이 코드를 줄일 수 있습니다.

### 플러그인 추가
위에 만든 Agent 클래스를 사용하기 위해 jar로 패키징하면서 jar 파일 안에다가 특정한 값들을 넣어줘야 합니다. 그러러면 maven을 통해 jar를 묶을 때 Manifest를 조작할 수 있는 maven-jar-plugin을 플러그인에 추가해야 합니다.
```
<build>
    <plugins>
        <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>3.1.2</version>
        <configuration>
            <archive>
            <index>true</index>
            <manifest>
                <addClasspath>true</addClasspath>
            </manifest>
            <manifestEntries>
                <mode>development</mode>
                <url>${project.url}</url>
                <key>value</key>
                <Premain-Class>me.whiteship.MasulsaAgent</Premain-Class>
                <Can-Redefine-Classes>true</Can-Redefine-Classes>
                <Can-Retransform-Classes>true</Can-Retransform-Classes>
            </manifestEntries>
            </archive>
        </configuration>
        </plugin>
    </plugins>
</build>
```
```
<Premain-Class>me.whiteship.MasulsaAgent</Premain-Class>
```
  * premain 방식 적용
```
<Can-Redefine-Classes>true</Can-Redefine-Classes>
<Can-Retransform-Classes>true</Can-Retransform-Classes>
```
  * 클래스를 바꾸는 작업이기 때문에 위 값들을 true로 추가해줘야 합니다.

### 패키징
이제 이 프로젝트를 패키징 합니다.
```
mvn clean package
```

![4](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/4.PNG)

![5](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/5.PNG)
* 빌드에 성공하면 jar 파일이 생깁니다.

jar 파일의 확장자를 zip으로 바꾸면 그 안에 있는 MANIFEST.MF 파일을 확인할 수 있습니다.   
![6](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/6.PNG)
* 우리가 설정했던 값들이 추가된 것을 확인할 수 있습니다.

이제 다시 빌드해서 다시 jar 파일을 생성해줍니다.
![7](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/7.PNG)
* 표시한 경로를 복사합니다.

### Javaagent 붙여서 사용하기
원래 프로젝트로 이동한 뒤 JVM Option에 아래 내용을 추가해줍니다.
```
-javaagent:C:\workspace_sts\MasulsaAgent\target\MasulsaAgent-1.0-SNAPSHOT.jar
```
* 위에서 복사한 경로를 붙여주면 됩니다.
* 여러개 추가할 수 있습니다.

![8](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/8.PNG)

이제 아래 코드를 실행해봅니다.
```
public class Masulsa {

    public static void main(String[] args) {
        System.out.println(new Moja().pullOut());
    }
}
```
![9](https://raw.githubusercontent.com/smpark1020/tistory/master/Java/javaagent%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/9.PNG)
* "Rabbit!"이 출력됩니다.

```
package me.smpaaark;

public class Moja {
    public Moja() {
    }

    public String pullOut() {
        return "";
    }
}
```
* 클래스파일은 변경되지 않았습니다.

이 방식은 클래스로더가 클래스를 읽어올 때 javaagent를 거쳐서 변경된 바이트코드를 읽어들여 사용합니다.
* 우리가 볼때는 바이트코드가 안바뀌어있지만 메모리에는 바뀌어 있는 것입니다. (Transparent - 비침투적)

## 참조
* [더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)