# 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라

두 가지 이상의 의미를 표현할 수 있으며, 그중 현재 표현하는 의미를 태그 값으로 알려주는 클래스가 존재한다.

```java
class Figure {
    enum Shape { RECTANGLE, CIRCLE };
    
    // 태그 필드
    final Shape shape;
    
    // 다음 필드는 모양이 사각형(RECTANGLE)일 때만 쓰인다.
    double length;
    double width;
    
    // 다음 필드는 모양이 원(CIRCLE)일 때만 쓰인다.
    double radius;
    
    // 사각형용 생성자
    ...
    
    // 원용 생성자
    ...
}
```

### 태그 달린 클래스의 단점
1. 열거 타입 선언, 태그 필드, switch 문 등 쓸데없는 코드가 많다.
2. 여러 구현이 한 클래스에 혼합되어 있어서 가독성이 나쁘다.
3. 다른 의미를 위한 코드도 존재하여 메모리도 많이 사용한다.
4. 필드들을 final 로 선언하려면 해당 의미에 쓰이지 않는 필드들까지 생성자에서 초기화해야 한다.
5. 또 다른 의미를 추가하려면 코드를 수정해야 한다.
6. 인스턴스 타입만으로는 현재 나타내는 의미를 알 수 없다.

**태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다.**

자바와 같은 객체 지향 언어는 타입 하나로 다양한 의미의 객체를 표현하는 훨씬 나은 수단을 제공한다.  
바로 클래스 계ㅈ층구조를 활용하는 서브타이핑(subtyping) 이다.

### 태그 달린 클래스를 계층구조로 바꾸는 방법
1. 계층구조의 root 가 될 추상 클래스를 정의한다.
2. 태그 값에 따라 동작이 달라지는 메서드들을 root 클래스의 추상 메서드로 선언한다.
3. 태그 값에 상관없이 동작이 일정한 메서드들을 root 클래스에 일반 메서드로 추가한다.
4. 모든 하위 클래스에서 공통으로 사용하는 데이터 필드들도 전부 root 클래스로 올린다.
5. root 클래스를 확장한 구체 클래스를 의미별로 하나씩 정의한다.
6. 각 하위 클래스에는 각자의 의미에 해당하는 데이터 필드들을 넣는다.
7. root 클래스가 정의한 추상 메서드들을 각자의 의미에 맞게 구현한다.

```java
abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
    final double radius;
    
    Circle(double radius) { this.radius = radius; }
    
    @Override
    double area() { return Math.PI * (radius * radius); }
}

class Rectangle extends Figure {
    final double length;
    final double width;
    
    Rectangle(double length, double width) { 
        this.length = length;
        this.width = width;
    }
    
    @Override
    double area() { return length * width; }
}
```