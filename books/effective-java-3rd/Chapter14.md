# 14. Comparable 을 구현할지 고려하라

compareTo 는 단순 동치성 비교에 더해 순서까지 비교할 수 있으며, 제네릭하다.  

Comparable 을 구현했다는 것은 그 클래스의 인스턴스들에는 자연적인 순서(natural order) 가 있음을 뜻한다.
Comparable 을 구현한 객체들의 배열은 다음처럼 손쉽게 정렬할 수 있다.  

Arrays.sort(a);

검색, 극단값 계산, 자동 정렬되는 컬렉션 관리도 역시 쉽게 할 수 있다.  
알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면 반드시 Comparable 인터페이스를 구현하자.

```java
public interface Comparable<T> {
    int compareTo(T t);
}
```

### compareTo 의 일반 규약

이 객체와 주어진 객체의 순서를 비교한다. 이 객체가 주어진 객체보다 작으면 음수를, 같으면 0을, 크면 양수를 반환한다.  
이 객체와 비교할 수 없는 타입의 객체가 주어지면 ClassCastException 을 던진다.

다음 설명에서 sgn(표현식) 표기는 수학에서 말하는 부호 함수(signum function) 를 뜻하며, 표현식의 값이 음수, 0, 양수일 때, -1, 0, 1 을 반환하도록 정의했다.

- Comparable 을 구현한 클래스는 모든 x, y 에 대해 sgn(x.compareTo(y)) == -sgn(y.compareTo(x)) 여야 한다.
- Comparable 을 구현한 클래스는 추이성을 보장해야 한다. 즉, x.compareTo(y) > 0 && y.compareTo(z) > 0 이면 x.compareTo(z) > 0 이다.
- Comparable 을 구현한 클래스는 모든 z 에 대해 x.compareTo(y) == 0 이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z)) 이다.
- 이번 권고는 필수는 아니지만 꼭 지키는게 좋다. (x.compareTo(y) == 0) == (x.equals(y)) 여야 한다. Comparable 을 구현하고 이 권고를 지키지 않는 모든 클래스는 그 사실을 명시해야 한다.

compareTo 메서드에서 필드의 값을 비교할 때는 핵심 필드부터 비교하며 관계 연산자 '<' 와 '>' 는 지양하고 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용하자.