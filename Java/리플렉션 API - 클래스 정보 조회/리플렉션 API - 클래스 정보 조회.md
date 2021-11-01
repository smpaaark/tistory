## 리플렉션의 시작은 Class<T>
리플렉션은 Class<T> API를 사용하면 됩니다. 여러가지 메서드를 통해 Class에 있는 필드, 상위 클래스, 구현하고 있는 인터페이스, 메서드 목록 등에 접근할 수 있습니다.

## Class<T>에 접근하는 방법
테스트를 위해 몇 가지 클래스를 정의하겠습니다.

**Book.java**
```
public class Book {

    private static String B = "Book";
    private static final String C = "Book";

    private String a = "a";
    public String d = "d";
    protected String e = "e";

    public Book() {
    }

    public Book(String a, String d, String e) {
        this.a = a;
        this.d = d;
        this.e = e;
    }

    private void f() {
        System.out.println("F");
    }

    public void g() {
        System.out.println("g");
    }

    public int h() {
        return 100;
    }
}
```
* 여러 종류의 필드, 생성자, 메서드를 정의합니다.

**MyInterface.java**
```
public interface MyInterface {
}
```
* 인터페이스를 테스트하기 위해 작성했습니다.

**MyBook.java**
```
public class MyBook extends Book implements MyInterface {
}
```
* Book 클래스를 상속받고, MyInterface를 구현합니다.

클래스 정보들에 접근하려면 Class<T>가 있어야 합니다. 모든 클래스 로딩이 끝나면 Class<T>의 인스턴스가 힙 영역에 생깁니다. 

Class<T>에 접근하는 방법은 크게 3가지가 있습니다.
```
public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<Book> bookClass = Book.class; // 1

        Book book = new Book();
        Class<? extends Book> aClass = book.getClass(); // 2

        Class<?> aClass1 = Class.forName("me.smpaaark.Book"); // 3
    }
}
```
* Class<Book> bookClass = Book.class;
  * "타입.class"로 접근할 수 있습니다.
* Class<? extends Book> aClass = book.getClass();
  * 모든 인스턴스는 getClass() 메소드를 가지고 있습니다.
  * "인스턴스.getClass()"로 접근할 수 있습니다.
* Class<?> aClass1 = Class.forName("me.smpaaark.Book");
  * 문자열로 읽어올 수 있습니다.
  * Class.forName("FQCN")
    * FQCN: 풀 패키지 경로 + 클래스 명
  * 클래스패스에 해당 클래스가 없다면 ClassNotFoundException이 발생합니다.

## Class<T>를 통해 할 수 있는 것
### 필드 (목록) 가져오기
**getFields()**
```
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<Book> bookClass = Book.class;

        Arrays.stream(bookClass.getFields()).forEach(System.out::println);
    }
}
```

![1]()
* d 필드만 출력이 됩니다.
* getFields()는 public한 것만 리턴합니다.

**getDeclaredFields()**
```
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<Book> bookClass = Book.class;

        Arrays.stream(bookClass.getDeclaredFields()).forEach(System.out::println);
    }
}
```

![2]()
* 모든 필드가 출력됩니다.
* 파라미터로 필드명을 전달하면 해당 필드를 리턴합니다.

```
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<Book> bookClass = Book.class;

        Book book = new Book();
        Arrays.stream(bookClass.getDeclaredFields()).forEach(f -> {
            try {
                f.setAccessible(true);
                System.out.printf("%s %s\n", f, f.get(book));
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        });
    }
}
```
* f.setAccessible(true);
  * 접근 제어자를 무시하도록 설정하는 메서드입니다.
  * 따라서 private에 접근이 가능합니다.
* f.get(book)
  * 해당 필드의 값을 가져옵니다.
* Book book = new Book();
  * 이 때 Book 인스턴스가 필요합니다.
  * 리플렉션으로 인스턴스를 만드는 것도 가능합니다.

![3]()
* 필드 정보와 해당 필드의 값이 출력됩니다.

### 메소드 (목록) 가져오기
**getMethods()**
```
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<Book> bookClass = Book.class;

        Book book = new Book();
        Arrays.stream(bookClass.getMethods()).forEach(System.out::println);
    }
}
```

![4]()
* 직접 정의한 메서드뿐만 아니라 Object에 정의되어 있는 메서드들도 함께 출력됩니다.

### 생성자 가져오기
**getDeclaredConstructors()**
```
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<Book> bookClass = Book.class;
        Arrays.stream(bookClass.getDeclaredConstructors()).forEach(System.out::println);
    }
}
```

![5]()

### 상위 클래스 가져오기
**getSuperclass()**
```
public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<? super MyBook> superclass = MyBook.class.getSuperclass();
        System.out.println(superclass);
    }
}
```

![6]()

### 인터페이스 (목록) 가져오기
**getInterfaces()**
```
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Class<?>[] interfaces = MyBook.class.getInterfaces();
        Arrays.stream(interfaces).forEach(System.out::println);
    }
}
```

![7]()

### 접근제어자 확인하기
```
import java.lang.reflect.Modifier;
import java.util.Arrays;

public class App {

    public static void main( String[] args ) throws ClassNotFoundException {
        Arrays.stream(Book.class.getDeclaredFields()).forEach(f -> {
            int modifiers = f.getModifiers();
            System.out.println(f);
            System.out.println(Modifier.isPrivate(modifiers));
            System.out.println(Modifier.isStatic(modifiers));
        });
    }
}
```
* f.getModifiers();
  * 해당 필드의 접근제어자를 가져옵니다.
* Modifier.isPrivate(modifiers)
  * 해당 접근제어자가 private인지 확인합니다.
* Modifier.isStatic(modifiers)
  * 해당 접근제어자가 static인지 확인합니다.

![8]()

이 밖에도 메서드, 애노테이션 관련 정보 등 수많은 정보들을 참조할 수 있습니다.

## 참조
* [더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)