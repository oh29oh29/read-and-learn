# 06. 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매번 생성하기 보다는 객체 하나를 재사용하자.  

생성 비용이 비싼 객체를 반복해서 필요하다면 캐싱하여 재사용하는것이 좋다.  

```java
class RomanNumerals {
    static boolean isRomanNumeral(String s) {
        return s.matches("정규표현식");
    }
}
```

String.matches 는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않다.  
이 메서드가 내부에서 만드는 Pattern 인스턴스는 생성 비용이 높다.  
따라서, 아래와 같이 미리 초기화 단계에서 캐싱해놓고 사용하는것이 좋다.  

```java
import java.util.regex.Pattern;

class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile("정규표현식");
    
    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```

성능만 좋아진 것이 아니라 코드도 더 명확해졌다.  

불필요한 객체를 만들어내는 또 다른 예로 오토방식을 들 수 있다.  
박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토방식이 일어나지 않도록 주의하자.  

다만, 모든 상황에서 객체 하나를 재사용하는것도 상황에 따라서 주의가 필요하다.  
이번 주제는 방어적 복사([50. 적시에 방어적 복사본을 만들라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter50.md))와 대조적이다.  
방어적 복사가 필요한 상황에서 객체를 재사용했을 때의 피해가, 필요 없는 객체를 반복 생성했을 때의 피해보다 훨씬 크다는 사실도 명심하자.