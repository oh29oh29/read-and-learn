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