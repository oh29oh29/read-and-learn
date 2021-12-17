# 05. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

많은 클래스가 하나 이상의 자원에 의존한다.  
사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식은 적합하지 않다.  
이 자원들을 클래스가 직접 만들게 해서도 안 된다.  

클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야 한다.  
이 조건을 만족하는 방식은 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식이다.  

```java
import java.util.Objects;

public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    ...
}
```

불변([17. 변경 가능성을 최소화하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter17.md)) 을 보장하여 같은 자원을 사용하려는 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있다.  

의존 객체 주입은 생성자, 정적 팩토리([01. 생성자 대신 정적 팩터리 메서드를 고려하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter01.md)), 빌더([02. 생성자에 매개변수가 많다면 빌더를 고려하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter02.md)) 모두 응용할 수 있다.  

의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 아주 많은 큰 프로젝트에서는 코드를 어지럽게 만들기도 한다.