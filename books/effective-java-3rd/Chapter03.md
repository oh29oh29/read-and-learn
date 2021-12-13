# 03. private 생성자나 열거 타입으로 싱글턴임을 보증하라

싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

## 싱글턴을 만드는 방식

보통 둘 중 하나다. 두 방식 모두 생성자는 private 으로 감춰두고,  
유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둔다.

### public static final 필드 방식

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();

    private Elvis() {
    }
    ...
}
```

public 이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.  
클라이언트는 손 쓸 방법이 없다. 예외는 단 한가지, 권한이 있는 클라이언트는 리플렉션 API([65. 리플렉션보다는 인터페이스를 사용하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter65.md)) 인 AccessibleObject.setAccessible 을 사용해 private 생성자를 호출할 수 있다.  
이러한 공격을 방어하려면 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.

#### 장점

- 해당 클래스가 싱글턴임이 API 에 명백히 드러난다.
- public static 필드가 final 이니 절대로 다른 객체를 참조할 수 없다 = 간결함

## 정적 팩토리 방식

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();

    private Elvis() {
    }

    public static Elvis getInstance() {
        return INSTNACE;
    }
    ...
}
```

Elvis.getInstance() 는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스란 결코 만들어지지 않는다.  
역시 리플렉션을 통한 예외는 똑같이 적용된다.

#### 장점

- API 를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
- 정적 팩토리를 제네릭 싱글 팩토리로 만들 수 있다. ([30. 이왕이면 제네릭 메서드로 만들라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter30.md))
- 정적 팩토리의 메서드 참조를 supplier 로 사용할 수 있다 (Elvis::getInstance 를 Supplier<Elvis> 로 사용) ([43. 람다보다는 메서드 참조를 사용하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter43.md), [44. 표준 함수형 인터페이스를 사용하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter44.md))

둘 중 하나의 방식으로 만든 싱글턴 클래스를 직렬화하려면 단순히 Serializable 을 구현한다고 선언하는 것만으로는 부족하다.  
모든 인스턴스 필드를 일시적(transient)이라고 선언하고 readResolve 메서드를 제공해야한다. ([89. 인스턴스 수를 통제해야 한다면 readResolve 보다는 열거 타입을 사용하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter89.md))  
이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.

```java
private Object readResolve(){
    // '진짜' Elvis 를 반환하고, 가짜 Elvis 는 GC 에 맡긴다.
    return INSTANCE;
}
```

## 열거 타입

싱글턴을 만드는 세 번째 방법은 원소가 하나인 열거 타입을 선언하는 것이다.

```java
public enum Elvis {
    INSTANCE;
    ...
}
```

public 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화할 수 있으며 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.  
대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.  
단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.