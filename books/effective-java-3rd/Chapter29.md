# 29. 이왕이면 제네릭 타입으로 만들라

일반 클래스를 제네릭 클래스로 만드는 첫 단계는 클래스 선언에 타입 매개변수를 추가하는 일이다.

```java
public class Stack {
    private Object[] elements;
    
    ...
}
```
```java
public class Stack<E> {
    private E[] elements;
    
    ...
}
```

다만, `E`와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.

#### 해결책

첫 번째, 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하는 방법이다.  
Object 배열을 생성한 다음 제네릭 배열로 형변환을 하는 것이다. 다만, 일반적으로 타입 안전하지 않다.

두 번째, `elements` 필드의 타입을 `E[]` 에서 `Object[]` 로 바꾸는 것이다.

첫 번째 방법은 가독성이 더 좋다. 첫 번째 방식에서는 형변환을 배열 생성 시 단 한 번만 해주면 되지만, 두 번째 방식에서는 배열에서 원소를 읽을 때마다 해줘야 한다.
따라서 첫 번째 방식이 더 선호되지만 배열의 런타임 타입이 컴파일타임 타입과 달라 힙 오염(heap pollution)을 일으킨다.

### 한정적 타입 매개변수

타입 매개변수에 제약을 두는 제네릭 타입도 있다.

```java
class DelayQueue<E extends Delayed> implements BlockingQueue<E>
```

타입 매개변수 목록인 `<E extends Delayed>` 는 `java.util.concurrent.Delayed` 의 하위 타입만 받는다는 뜻이다.  
이렇게 하여 DelayQueue 자신과 DelayQueue 를 사용하는 클라이언트는 DelayQueue 의 원소에서 형변환 없이 곧바로 Delayed 클래스의 메서드를 호출할 수 있다.  

`ClassCastException` 걱정은 할 필요가 없다.  
이러한 타입 매개변수 `E` 를 한정적 타입 매개변수라 한다.