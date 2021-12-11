## 01. 생성자 대신 정적 팩터리 메서드를 고려하라

```java
public final class Boolean implements java.io.Serializable,
        Comparable<Boolean> {

    /**
     * The {@code Boolean} object corresponding to the primitive
     * value {@code true}.
     */
    public static final Boolean TRUE = new Boolean(true);

    /**
     * The {@code Boolean} object corresponding to the primitive
     * value {@code false}.
     */
    public static final Boolean FALSE = new Boolean(false);

    ...

    public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
    
    ...
}
```

### 장점
1. 이름을 가질 수 있다.
    - 생성자 자체만으로는 의미를 알기 어렵지만 정적 팩터리는 이름만 잘 지으면 의미를 나타낼 수 있다.
2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

### 단점
1. 상속을 하려면 public 이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

#### 정적 팩터리 메서드에 흔히 사용하는 명명 방식
- from  
  매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드  
  ex. Date d = Date.from(instant);

- of  
  여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드  
  ex. Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
  
- valueOf  
  from 과 of 의 더 자세한 버전  
  ex. BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
  
- instance 혹은 getInstance  
  매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.  
  ex. StackWalker luke = StackWalker.getInstance(options);
  
- create 혹은 newInstance  
  instance 혹은 getInstance 와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.  
  ex. Object newArray = Array.newInstance(classObject, arrayLen);
  
- getType  
  getInstance 와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. 'Type' 은 반환할 타입이다.  
  ex. FileStore fs = Files.getFileStore(path);
  
- newType  
  newInstance 와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. 'Type' 은 반환할 타입이다.  
  ex. BufferedReader br = Files.newBufferedReader(path);
  
- type  
  getType 과 newType 의 간결한 버전  
  ex. List<Complaint> litany = Collections.list(legacyLitany);
