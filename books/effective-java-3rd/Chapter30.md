# 30. 이왕이면 제네릭 메서드로 만들라

클래스처럼 메서드도 제네릭으로 만들 수 있다.  
매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다.  

##### 위험한 로 타입 메서드
```java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

위 코드는 컴파일은 되지만 경고가 발생한다.  
경고를 없애려면 이 메서드를 타입 안전하게 만들어야 한다.

##### 제네릭 메서드
```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

위 메서드는 경고 없이 컴파일되며, 타입 안전하고, 쓰기도 쉽다.

```java
public static void main(String[] args) {
    Set<String> guys = Set.of("톰", "딕", "해리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guys, stooges);
}
```

`union` 메서드는 집합 3개(입력 2개, 반환 1개)의 타입이 모두 같아야 한다.  
이를 한정적 와일드카드 타입을 사용하여 더 유연하게 개선할 수 있다.

### 제네릭 싱글턴 팩터리

제네릭은 런타임에 타입 정보가 소거되므로 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다.  
하지만 이렇게 하려면 요청한 타입 매개변수에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야한다.  
이 패턴을 제네릭 싱글턴 팩터리라 하며, `Collections.reverseOrder` 같은 함수 객체나 `Collections.emptySet` 같은 컬렉션용으로 사용한다.

identity function 을 담은 클래스를 만들어야 한다고 해보자.
```java
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

identity function 객체는 상태가 없으니 요청할 때마다 새로 생성하는 것은 낭비다.  
자바의 제네릭이 실체화된다면 identity function 을 타입별로 하나씩 만들어야 했겠지만, 소거 방식을 사용한 덕에 제네릭 싱글턴 하나면 충분한다.

`T` 가 어떤 타입이든 `UnaryOperator<Object>` 는 `UnaryOperator<T>` 가 아니기 때문에 형변환하면 비검사 형변환 경고가 발생한다.  
하지만 identity function 란 입력 값을 수정 없이 그대로 반환하는 특별한 함수이므로, `T` 가 어떤 타입이든 `UnaryOperator<T>` 를 사용해도 타입 안전하다.

```java
public static void main(String[] args) {
    String[] strings = { "삼베", "대마", "나일론" };
    UnaryOperator<String> sameString = identityFunction();

    Integer[] numbers = { 1, 2.0, 3L };
    UnaryOperator<Integer> sameNumber = identityFunction();
}
```

### 재귀적 타입 한정 (recursive type bound)

드물긴 하지만, 자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정할 수 있다.  
주로 타입의 자연적 순서를 정하는 `Comparable` 인터페이스와 함께 쓰인다.

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

타입 매개변수 `T` 는 `Comparable<T>` 를 구현한 타입이 비교할 수 있는 원소의 타입을 정의한다.  
실제로 거의 모든 타입은 자신과 같은 타입의 원소와만 비교할 수 있다.  
따라서 `String` 은 `Comparable<String>` 을 구현하고 `Integer` 는 `Comparable<Integer>` 를 구현하는 식이다.

`Comparable` 을 구현한 원소의 컬렉션을 입력받는 메서드들은 주로 그 원소들을 정렬 혹은 검색하거나, 최솟값이나 최댓값을 구하는 식으로 사용된다.  
이 기능을 수행하려면 컬렉션에 담긴 모든 원소가 상호 비교될 수 있어야 한다.  

다음은 이런 제약을 코드로 만든것이다.

```java
public static <E extends Comparable<E>> E max(Collection<E> c);
```

타입 한정인 `<E extends Comparable<E>>` 는 "모든 타입 E 는 자신과 비교할 수 있다" 라고 읽을 수 있다.