# 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

```java
class Point {
    public double x;
    public double y;
}
```

이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지
못한다([15. 클래스와 멤버의 접근 권한을 최소화하라](https://github.com/oh29oh29/read-and-learn/tree/master/books/effective-java-3rd/Chapter15.md))
.

1. API 를 수정하지 않고는 내부 표현을 바꿀 수 없다.
2. 불변식을 보장할 수 없다.
3. 외부에서 필드에 접근할 때 부수 작업을 수행할 수 없다.

필드들을 모두 private 으로 바꾸고 public 접근자를 추가한다.  

```java
class Point {
    private double x;
    private double y;

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() {
        return this.x;
    }

    public double getY() {
        return this.y;
    }

    public void setX(double x) {
        this.x = x;
    }
    
    public void setY(double y) {
        this.y = y;
    }
}
```

패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.  

하지만, package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 문제가 없다.  

자바 플랫폼 라이브러리에도 public 클래스의 필드를 직접 노출하지 말라는 규칙을 어기는 사례가 존재한다.  
대표적인 예가 java.awt.package 패키지의 Point 와 Dimension 클래스이다.  
이 클래스들을 타산지석으로 삼을 필요가 있다.