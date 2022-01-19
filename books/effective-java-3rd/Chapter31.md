# 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

```java
public class Stack<E> {
    public Stack() { ... }
    public void push(E e) { ... }
    public E pop() { ... }
    public boolean isEmpty() { ... }
    
    public void pushAll(Iterable<E> src) {
        for (E e : src) {
            push(e);
        }
    }
}
```

`pushAll` 메서드는 컴파일되지만 완벽하진 않다.  
`Iterable src` 의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동한다. 하지만 `Stack<Number>` 로 선언한 후 `Integer` 타입인 `intVal` 을 사용해 `pushAll(intVal)` 을 호출하면 오류 메시지가 뜬다.  
매개변수화 타입이 불공변이기 때문이다.

자바는 이런 상황에 대처할 수 있는 한정적 와일드카드 타입이라는 특별한 매개변수화 타입을 지원한다.  
`pushAll` 의 입력 매개변수 타입은 'E 의 Iterable' 이 아니라 'E 의 하위 타입의 Iterable' 이어야 한다.  
와일드카드 타입 `Iterable<? extends E>` 가 이런 뜻이다.

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}
```

`Stack` 은 물론 이를 사용하는 클라이언트 코드도 깔끔하게 컴파일 된다.  
`Stack` 과 클라이언트 모두 깔끔하게 컴파일되었다는 것은 모든 것이 타입 안전하다는 뜻이다.

다음은 `Stack` 안의 모든 원소를 주어진 컬렉션으로 옮겨 담는 메서드를 작성했다.
```java
public void popAll(Collection<E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

이번에도 주어진 컬렉션의 원소 타입이 스택의 원소 타입과 일치한다면 깔끔하게 컴파일되고 문제없이 동작한다.  
하지만 이번에도 역시나 완벽하진 않다.  
`Stack<Number>` 의 원소를 `Object` 용 컬렉션으로 옮기려 한다고 할때, "Collection<Object> 는 Collection<Number> 의 하위 타입이 아니다" 라는 오류가 발생한다.  
이번에도 와일드카드 타입으로 해결할 수 있다.

`popAll` 의 입력 매개변수의 타입이 'E 의 Collection' 이 아니라 'E 의 상위 타입의 Collection' 이어야 한다.  
와일드카드 타입 `Collection<? super E>` 가 이런 뜻이다.

```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

**유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라.**  
다만, 입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 쓰지 말아야 한다.

매개변수화 타입 `T` 가 생산자라면 `<? extends T>` 를 사용하고, 소비자라면 `<? super T>` 를 사용하라.

와일드카드와 관련해 논의해야 할 주제가 하나 더 있다.  
타입 매개변수와 와일드카드에는 공통되는 부분이 있어서, 메서드를 정의할 때 둘 중 어느 것을 사용해도 괜찮을 때가 많다.  

다음 코드에서 첫 번째는 비한정적 타입 매개변수를 사용했고 두 번째는 비한정적 와일드카드를 사용했다.

```java
public statkc <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

public API 라면 간단한 두 번째 방법이 더 낫다.  
어떤 리스트든 이 메서드에 넘기면 명시한 인덱스의 원소들을 교환해줄 것이다. 신경 써야 할 타입 매개변수도 없다.

**메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라.**  
비한정적 타입 매개변수라면 비한정적 와일드카드로, 한정적 타입 매개변수라면 한정적 와일드카드로 바꾸면 된다.

하지만 두 번째 `swap` 선언에는 문제가 존재한다.  
다음과 같이 구현한 코드가 컴파일되지 않는다.

```java
public static void swap(List<?> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```

원인은 리스트의 타입이 `List<?>` 인데, `List<?` 에는 `null` 외에는 어떤 값도 넣을 수 없다는 데 있다.  
해결할 수 있는 방법이 있다. 와일드카드 타입의 실제 타입을 알려주는 메서드를 `private` 도우미 메서드로 따로 작성하여 활용하는 방법이다.  

```java
public static void swap(List<?> list, int i, int j) {
    swapHelper(list, i, j);
}

private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```

`swapHelper` 메서드는 리스트가 `List<E>` 임을 알고 있다.  
즉, 이 리스트에서 꺼낸 값의 타입은 항상 `E` 이고, `E` 타입의 값이라면 이 리스트에 넣어도 안전함을 알고 있다.