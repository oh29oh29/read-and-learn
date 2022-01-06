# 20. 추상 클래스보다는 인터페이스를 우선하라

기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.  
반면 기존 클래스 위에 새로운 추상 클래스를 끼워넣기는 어려운 게 일반적이다.

인터페이스는 믹스인(mixin) 정의에 알맞다.  
믹스인이란 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 '주된 타입' 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 준다.

```
Comparable 은 자신을 구현한 클래스의 인스턴스들끼리는 순서를 정할 수 있다고 선언하는 믹스인 인터페이스다.
```

현실에는 가수와 작곡가와 같은 계층을 엄격히 구분하기 어려운 개념이 있다.  
***인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있다.***  
타입을 인터페이스로 정의하면 한 클래스가 여러 인터페이스를 모두 구현해도 문제되지 않는다. 또는 인터페이스를 확장한 제 3의 인터페이스를 정의할 수도 있다.

```java
public interface Singer {
    AudioClip sing(Song s);
}
public interface Songwriter {
    Song compose(int chartPosition);
}
public interface SingerSongwriter extends Singer, Songwriter {
    AudioClip strum();
    void actSensitive();
}
```

래퍼 클래스 관용구([18. 상속보다는 컴포지션을 사용하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter18.md)) 와 함께 사용하면 
인터페이스는 기능을 향상시키는 안전하고 강력한 수단이 된다.

인터페이스의 메서드 중 구현 방법이 명백한 것이 있따면, 그 구현을 디폴트 메서드로 제공해 일감을 덜어줄 수 있다.
다만, 많은 인터페이스가 equals 와 hashCode 같은 Object 메서드를 정의하고 있지만, 이들은 디폴트 메서드로 제공해서는 안 된다.

한편, ***인터페이스와 추상 골격 구현 클래스를 함께 제공하는 식으로 인터페이스와 추상 클래스의 장점을 모두 취하는 방법도 있다.***

인터페이스로는 타입을 정의하고, 골격 구현 클래스는 나머지 메서드들까지 구현한다.
이렇게 해두면 단순히 골격 구현을 확장하는 것만으로 이 인터페이스를 구현하는 데 필요한 일이 대부분 완료된다 (= 템플릿 메서드 패턴)

```java
[골격 구현을 사용해 완성한 구체 클래스]
static List<Integer> intArrayAsList(int[] a) {
    Objects.requireNonNull(a);
    
    return new AbstractList<>() {
        @Override
        public Integer get(int i) {
            return a[i];
        }
        
        @Override
        public Integer set(int i, Integer val) {
            int oldVal = a[i];
            a[i] = val;
            return oldVal;
        }
        
        @Override
        public int size() {
            return a.length;
        }
    }
}
```

골격 구현 클래스의 아름다움은 추상 클래스처럼 구현을 도와주는 동시에, 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서는 자유롭다는 점이다.

골격 구현은 기본적으로 상속해서 사용하는 걸 가정하므로 설계 및 문서화 지침([19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter19.md)) 을 모두 따라야 한다.
