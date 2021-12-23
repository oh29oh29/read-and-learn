# 10. equals 는 일반 규약을 지켜 재정의하라

equals 메서드 재정의는 잘못하면 문제가 발생할 수 있다.   
문제를 회피하는 가장 쉬운 방법은 재정의하지 않는 것이다.

따로 재정의하지 않으면 인스턴스는 오직 자기 자신과만 같게 된다.  
다음과 같은 상황 중 하나에 해당한다면 재정의하지 않는 것이 최선이다.  

- 각 인스턴스가 본질적으로 고유하다.
- 인스턴스의 '논리적 동치성(logical equality)'을 검사할 일이 없다.
- 상위 클래스에서 재정의한 equals 가 하위 클래스에도 적합하다.
- 클래스가 private 이거나 package-private 이고 equals 메서드를 호출할 일이 없다.

그렇다면 재정의해야 할 때는 언제일까?

- 객체 식별성(object identity; 두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals 가 논리적 동치성을 비교하도록 재정의되지 않았을 때다.  

equals 메서드를 재정의할 때는 반드시 일반 규약을 따라야 한다.  
equals 메서드는 동치관계(equivalence relation) 을 구현하며, 다음을 만족한다.

- 반사성(reflexivity): null 이 아닌 모든 참조 값 x 에 대해, x.equals(x) 는 true 이다.
- 대칭성(symmetry): null 이 아닌 모든 참조 값 x, y 에 대해, x.equals(y) 가 true 면 y.equals(x) 도 true 이다.
- 추이성(transitivity): null 이 아닌 모든 참조 값 x, y, z 에 대해, x.equals(y) 가 true 이고 y.equals(z) 도 true 이면 x.equals(z) 도 true 이다.  
- 일관성(consistency): null 이 아닌 모든 참조 값 x, y 에 대해, x.equals(y) 를 반복해서 호출하면 항상 true 이거나 항상 false 이다.
- null-아님: null 이 아닌 모든 참조 값 x 에 대해, x.equals(null) 은 false 이다.

위의 규약을 종합해서 적합한 equals 메서드 구현 방법은 다음과 같다.

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
3. 입력을 올바른 타입으로 형변환한다.
4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다.

equals 메서드 재정의에 대하여 주의사항도 존재한다.

1. equals 를 재정의할 땐 hashCode 도 반드시 재정의하자
2. 너무 복잡하게 해결하려 하지말고 필드들의 동치성만 검사해도 규약을 쉽게 지킬 수 있다.
3. Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자. (재정의가 아닌 다중정의이다)