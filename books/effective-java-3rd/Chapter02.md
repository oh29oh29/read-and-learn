# 02. 생성자에 매개변수가 많다면 빌더를 고려하라

정적 팩토리와 생성자에는 선택적 매개변수가 많을 때 적절히 대응하기 어렵다는 제약이 존재한다.

선택적 매개변수가 많을 때 대안

1. 점층적 생성자 패턴 (telescoping constructor pattern)
    - 점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.
    - 코드를 읽을 때 각 값의 의미가 무엇인지 헷갈릴 것이고, 매개변수가 몇 개인지도 주의해서 코드를 작성해야 한다.


2. 자바빈즈 패턴 (JavaBeans pattern)
    - 매개변수가 없는 생성자로 객체를 만든 후, 세터 메서드들을 호출해서 원하는 매개변수의 값을 설정하는 방식이다.
    - 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체과 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.
    - 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없으며 쓰레드 안전성을 얻으려면 프로그래머가 추가 작업을 해줘야만 한다.

기존 대안들의 단점들을 보완한 (점층적 생성자 패턴의 안전성과 자바빈즈 패턴의 가독성을 겸비한) <b>빌더 패턴(Builder pattern)</b> 이 있다.

## 빌더 패턴 (Builder pattern)

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;
        
        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }
        
        public Builder calories(int val) { 
            this.calories = val;
            return this;
        }

       public Builder fat(int val) {
          this.fat = val;
          return this;
       }

       public Builder sodium(int val) {
          this.sodium = val;
          return this;
       }

       public Builder carbohydrate(int val) {
          this.carbohydrate = val;
          return this;
       }
       
       public NutritionFacts build() {
            return new NutritionFacts(this);
       }
       
       private NutritionFacts(Builder builder) {
            this.servingSize = builder.servingSize;
            this.servings = builder.servings;
            this.calories = builder.calories;
            this.fat = builder.fat;
            this.sodium = builder.sodium;
            this.carbohydrate = builder.carbohydrate;
       }
    }
}
```

### 생성 순서

1. 필수 매개변수만으로 생성자(혹은 정적 팩토리)를 호출해 빌더 객체를 얻는다.
2. 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정한다.
3. 매개변수가 없는 build() 메서드를 호출해 필요한 객체를 얻는다.

### 장점

1. 가변인수(varargs) 매개변수를 여러 개 사용할 수 있다.
2. 메서드를 여러 번 호출하도록 하고 각 호출 때 넘겨진 매개변수들을 하나의 필드로 모을 수도 있다.
3. 상당히 유연하다. 빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있다.
4. 객체마다 부여되는 일련번호와 같은 특정 필드는 빌더가 알아서 채우도록 할 수도 있다.

### 단점

1. 객체를 만들려면, 빌더부터 만들어야 한다. (성능에 민감한 상황에서는 문제가 될 수 있다)
2. 점층적 생성자 패턴보다 코드가 장황해서 매개변수가 4개 이상은 되어야 가치가 있다.
