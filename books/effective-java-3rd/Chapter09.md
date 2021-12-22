# 09. try-finally 보다는 try-with-resources 를 사용하라

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다.  
자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제로 이어지기도 한다.

전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 try-finally 가 쓰였다.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.nio.Buffer;

class TryFinally {
    static String firstLineOfFile(String path) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader(path));
        
        try {
            return br.readLine();
        } finally {
            br.close();
        }
    }
}
```

하지만 이런 try-finally 방식에는 단점이 존재한다.
1. try 문에서 예외가 발생하고 그 영향으로 finally 에서도 오류가 발생하면 디버깅하기 까다로울 수 있다.
2. 자원이 둘 이상이면 코드가 지저분해진다.

### try-with-resources

자바 7 부터 추가된 try-with-resources 덕에 이런 부분들이 개선되었다.  
해당 자원이 AutoCloseable 인터페이스를 구현하면 사용 가능하다.  

```java
class TryWithResources {
    static String firstLineOfFile(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            return br.readLine();
        }
    }
}
```

readLine 과 close 호출 양쪽에서 예외가 발생하면, close 에서 발생한 예외는 숨겨지고 readLine 에서 발생한 예외가 기록된다.  

보통의 try-finally 에서처럼 catch 절을 쓸 수 있다. catch 절 덕분에 try 문을 중첩하지 않고도 다수의 예외를 처리할 수 있다.