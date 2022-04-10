# 01. 객체, 설계

### 티켓 판매 애플리케이션 구현하기

소극장의 주인이 되어 경영하고 있다고 상상하자.  
현재 다음과 같은 상황에 있다.

1. 이벤트를 통해 무료 초대장을 발송
2. 이벤트에 당첨되어 무료 초대장을 갖고 있는 관람객과 그렇지 못한 관람객을 구분
3. 무료 초대장은 티켓으로 교환한 후 입장
4. 무료 초대장이 없는 관람객은 티켓을 구매한 후 입장

```java
public class Invitation {
    private LocalDateTime when;
}
```

```java
public class Ticket {
    private Long fee;

    public Long getFee() {
        return fee;
    }
}
```
<br>

관람객이 가지고 올 수 있는 소지품은 초대장, 현금, 티켓이다.  
관람객은 소지품을 보관할 용도로 가방을 들고 올 수 있다.

```java
public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;

    public boolean hasInvitation() {
        return invitation != null;
    }

    public boolean hasTicket() {
        return ticket != null;
    }
    
    public void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }
    
    public void minusAmount(Long amount) {
        this.amount -= amount;
    }
    
    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

<br>

이벤트 당첨된 관람객의 가방 안에는 현금과 초대장이 존재한다.  
그렇지 않은 관람객의 가방 안에는 현금만 존재한다.  
`Bag` 의 인스턴스를 생성하는 시점에 이 제약을 강제한다.

```java
public class Bag {
    public Bag(Long amount) {
        this(null, amount);
    }
    
    public Bag(Invitation invitation, Long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }
}
```

<br>

관람객은 가방을 소지할 수 있다.

```java
public class Audience {
    private Bag bag;
    
    public Audience(Bag bag) {
        this.bag = bag;
    }
    
    public Bag getBag() {
        return bag;
    }
}
```

<br>

관람객은 매표소에서 초대장을 티켓으로 교환 또는 구매해야 한다.  
매표소는 판매할 티켓과 판매 금액이 보관되어 있다.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket... tickets) {
        this.amount = amount;
        this.tickets.addAll(Collections.singletonList(tickets));
    }
    
    public Ticket getTicket() {
        return tickets.remove(0);
    }
    
    public void minusAmount(Long amount) {
        this.amount -= amount;
    }
    
    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

<br>

판매원은 매표소에서 초대장을 티켓으로 교환 또는 판매하는 역할을 수행한다. 
판매원은 자신이 일하는 매표소를 알고 있어야 한다.

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```

<br>

소극장은 관람객을 맞이할 수 있다.

```java
public class Theater {
    private TicketSeller ticketSeller;
    
    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }
    
    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

### 무엇이 문제인가?

클린코드의 저자 로버트 마틴은 모든 소프트웨어 모듈에는 세 가지 목적이 있다고 한다.
1. 실행 중에 제대로 동작하는 것이다.
2. 변경을 위해 존재한다.
3. 코드를 읽는 사람과 의사소통하는 것이다.

앞에서 작성한 프로그램은 첫 번째 제약은 만족시키지만 나머지 목적은 만족시키지 못한다.

#### 예상을 빗나가는 코드

이해 가능한 코드란 그 동작이 우리의 예상에서 크게 벗어나지 않는 코드다.  
앞에서 본 예제는 예상을 벗어난다.

앞에서 작성한 프로그램의 문제는 관람객과 판매원이 소극장의 통제를 받는 수동적인 존재라는 점이다.  
현실에서는 능동적인 존재이다. (하지만 코드안에서는 그렇게 하지 않는다)

코드를 이해하기 어렵게 만드는 또 다른 이유가 있다.  
이 코드를 이해하기 위해서는 여러 가지 세부적인 내용들을 한꺼번에 기억해야 한다.  

#### 변경에 취약한 코드

가장 큰 문제는 변경에 취약하다는 것이다.  

관람객이 가방을 들고 있다는 가정이 변경된다면, `Audience` 클래스에서 `Bag` 을 제거해야 할뿐만 아니라 `Audience`의 `Bag` 에 직접 접근하는 모든 메서드 역시 수정해야 한다.  
다른 클래스가 `Audience` 의 내부에 대해 더 많이 알면 알수록 `Audience` 를 변경하기 어려워진다.

객체 사이의 의존성(dependency)과 관련된 문제다.  
의존성은 어떤 객체가 변경될 때 그 객체에게 의존하는 다른 객체도 함께 변경될 수 있다는 것을 의미한다.

의존성을 완전히 없애는 것이 정답은 아니다.  
객체지향 설계는 서로 의존하면서 협력하는 객체들의 공동체를 구축하는 것이다.  
최소한의 의존성만 유지하고 불필요한 의존성을 제거하는게 최선이다.

객체 사이의 의존성이 과한 경우를 결합도(coupling)가 높다고 말한다.  
반대로 의존성이 합리적인 수준일 경우 결합도가 낮다고 말한다.

결합도가 높으면 함께 변경될 가능성도 높아지기 때문에 변경이 어려워진다.  
따라서 설계의 목표는 객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것이다.

### 설계 개선하기

해결 방법은 간단하다.  
`Theater`가 `Audience`와 `TicketSeller`에 관해 너무 세세한 부분까지 알지 못하도록 정보를 차단하면 된다.  

즉, 관람객과 판매원을 자율적인 존재로 만들면 되는 것이다.

#### 자율성을 높이자

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

`ticketOffice`를 외부에서 접근할 수 없도록 했다.  
따라서 `TicketSeller`는 `ticketOffice`에 대한 행위를 스스로 수행할 수밖에 없다.  

이처럼 개념적/물리적으로 객체 내부의 세부적인 사항을 감추는 것을 캡슐화(encapsulation)라고 한다.  
캡슐화의 목적은 변경하기 쉬운 객체를 만드는 것이다. 객체 간의 결합도를 낮출 수 있기 때문에 설계를 좀 더 쉽게 변경할 수 있게 된다.

```java
public class Theater {
    private TicketSeller ticketSeller;
    
    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }
    
    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
}
```

`Theater`는 오직 `TicketSeller`의 인터페이스에만 의존한다.  

이제는 `Audience`도 자율적인 객체로 만들어보자.

```java
public class Audience {
    private Bag bag;
    
    public Audience(Bag bag) {
        this.bag = bag;
    }
    
    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}
```

이제 `TicketSeller`가 `Audience`의 인터페이스에만 의존하도록 수정하자.

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```

#### 무엇이 개선됐는가

객체들이 예상대로 행동한다. 따라서 코드를 읽는 사람과의 의사소통이라는 관점에서 개선된 것으로 보인다.  
더 중요한 점은 내부 구현을 변경하더라도 의존하고 있던 객체에서 변경할 필요가 없어졌다는 것이다.

#### 어떻게 한 것인가

자기 자신의 문제를 스스로 해결하도록 코드를 변경한 것이다.  
객체의 자율성을 높이는 방향으로 설계를 개선했다.  

#### 캡슐화와 응집도

핵심은 객체 내부의 상태를 캡슐화하고 객체 간에 오직 메시지를 통해서만 상호작용하도록 만드는 것이다.

밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하는 객체를 가리켜 응집도(cohesion)가 높다고 말한다.  
객체의 응집도를 높이기 위해서는 객체 스스로 자신의 데이터를 책임져야 한다.

#### 절차지향과 객체지향

수정 전 코드 관점에서 `Theater`의 `enter` 메서드는 프로세스이며 `Audience`, `TicketSeller`, `Bag`, `TicketOffice`는 데이터다.  
이처럼 프로세스와 데이터를 별도의 모듈에 위치시키는 방식을 절차적 프로그래밍이라고 부른다.  
절차적 프로그래밍은 프로세스가 필요한 모든 데이터에 의존해야 한다는 근본적인 문제점 때문에 변경에 취약하다.

수정한 코드에서는 데이터를 사용하는 프로세스를 데이터를 소유하고 있는 `Audience`와 `TicketSeller` 내부로 옮겼다.  
이처럼 데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍하는 방식을 객체지향 프로그래밍이라고 부른다.
객체지향 코드는 자신의 문제를 스스로 처리해야 한다는 예상을 만족시켜주기 때문에 이해하기 쉽고, 객체 내부의 변경이 외부에 파급되지 않도록 제어 할 수 있기 때문에 변경이 수월하다.

#### 책임의 이동

두 방식 사이에 근본적인 차이를 만드는 것은 책임의 이동이다.

절차적 프로그래밍 방식으로 작성된 수정 전 코드에서는 책임이 `Theater`에 집중되어 있었다.  
객체지향 설계에서는 `Theater`에 집중되어 있던 책임이 개별 객체로 이동했다.

불필요한 세부사항을 캡슐화하는 자율적인 객체들이 낮은 결합도와 높은 응집도를 가지고 협력하도록 최소한의 의존성만을 남기는 것이 훌륭한 객체지향 설계다.

#### 더 개선할 수 있다

`Bag`를 자율적인 존재로 바꿔보자.

```java
public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;
    
    public Bag(Long amount) {
        this(null, amount);
    }

    public Bag(Invitation invitation, Long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }

    public Long hold(Ticket ticket) {
        if (hasInvitation()) {
            setTicket(ticket);
            return 0L;
        } else {
            setTicket(ticket);
            minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }

    private boolean hasInvitation() {
        return invitation != null;
    }

    private void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    private void minusAmount(Long amount) {
        this.amount -= amount;
    }
}
```

<br>

이제 `Audience`를 `Bag`의 구현이 아닌 인터페이스에만 의존하도록 수정하자.

```java
public class Audience {
    public Long buy(Ticket ticket) {
        return bag.hold(ticket);
    }
}
```

<br>

`TicketOffice`도 자율적인 객체로 만들어주자.

```java
public class TicketOffice {
    public void sellTicketTo(Audience audience) {
        plusAmount(audience.buy(getTicket()));
    }
    
    private Ticket getTicket() {
        return tickets.remove(0);
    }
    
    private void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

<br>

`TicketSeller`가 `TicketOffice`의 구현이 아닌 인터페이스에만 의존하게 되었다.

```java
public class TicketSeller {
    public void sellTo(Audience audience) {
        ticketOffice.sellTicketTo(audience);
    }
}
```

변경 후에는 `TicketOffice`가 `Audience`에게 직접 티켓을 판매하기 때문에 `Audience`에 관해 알고 있어야 한다.  
변경 전에는 존재하지 않았던 새로운 의존성이 추가된 것이다. 즉, `TicketSeller`의 자율성은 높였지만 전체 설계 관점에서 결합도가 상승했다.

어떤 기능을 설계하는 방법은 한 가지 이상일 수 있다.  
동일한 기능을 한 가지 이상의 방법으로 설계할 수 있기 결국 설계는 트레이드오프의 산물이다. 

#### 그래, 거짓말이다!

현실에서는 수동적인 존재라고 하더라도 일단 객체지향의 세계에 들어오면 모든 것이 능동적이고 자율적인 존재로 바뀐다.  
레베카 워프스브록은 이처럼 능동적이고 자율적인 존재로 소프트웨어 객체를 설계하는 원칙을 가리켜 의인화라고 부른다.

훌륭한 객체지향 설계란 소프트웨어를 구성하는 모든 객체들이 자율적으로 행동하는 설계를 가리킨다.  
그 대상이 실세계에서는 생명이 없는 수동적인 존재라고 하더라도 객체지향 세계에서는 생명과 지능을 가진 존재가 되는 것이다.

### 객체지향 설계

#### 설계가 왜 필요한가

설계는 코드를 작성하는 매 수간 코드를 어떻게 배치할 것인지를 결정하는 과정에서 나온다.  
설계는 코드 작성의 일부이며 코드를 작성하지 않고서는 검증할 수 없다.

좋은 설계란 오늘 요구하는 기능을 온전히 수행하면서 내일의 변경을 매끄럽게 수용할 수 있는 설계다.

#### 객체지향 설계

훌륭한 객체지향 설계란 협력하는 객체 사이의 의존성을 적절하게 관리하는 설계다.  
객체 간의 의존성은 애플리케이션을 수정하기 어렵게 만드는 주범이다.

데이터와 프로세스를 하나의 덩어리로 모으는 것은 훌륭한 객체지향 설계로 가는 첫걸음일 뿐이다.  
진정한 객체지향 설계로 나아가는 길은 협력하는 객체들 사이의 의존성을 적절하게 조절함으로써 변경에 용이한 설계를 만드는 것이다.