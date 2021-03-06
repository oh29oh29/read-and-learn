# 02. 리팩토링 개론

### 리팩토링은 무엇인가

1. (명사) 겉으로 드러나는 기능은 그대로 둔 채, 알아보기 쉽고 수정하기 쉽게 소프트웨어 내부를 수정하는 작업
2. (동사) 겉으로 드러나는 기능은 그대로 둔 채, 리팩토링 기법을 적용해서 소프트웨어 구조를 변경한다.

* 리팩토링의 목적은 소프트웨어를 더 이해하기 쉽고 수정하기 쉽게 만드는 것이다. 성능 최적화와 상반된다.
* 리팩토링은 겉으로 드러나는 소프트웨어 기능에 영향을 주지 않는다.

<br>

### 리팩토링은 왜 해야 하나

1. 설계 개선
    * 프로그램 설계는 시간이 지날수록 노후되고 구조가 복잡해지기 때문에 리팩토링을 통해 코드를 정리하여 소프트웨어 설계를 개선한다.
2. 가독성 향상
    * 리팩토링된 코드는 다른 개발자들이 보기에도 이해하기 쉽다. 
    * 낯선 코드를 리팩토링을 통해 쉽게 이해할 수 있다.
3. 버그 감소
    * 프로그램 구조를 명료하게 만들어서 버그 발견이 쉽다.
4. 프로그래밍 속도 향상
    * 깔끔한 설계는 전적으로 신속한 개발을 목적으로 한다.
    * 설계가 깔끔하지 않으면 개발 초기의 아주 잠시 동안엔 진행이 빠를지 몰라도 얼마 못 가서 개발 속도가 떨어진다.
    
<br>

### 리팩토링은 어떨 때 필요한가

1. 같은 작업을 3번할 때
2. 기능을 추가할 때
    * 코드를 이해하기 쉽게 만들기 위해서
    * 설계가 지저분해서 어떤 기능을 추가하기 힘들 때
3. 버그를 수정할 때
4. 코드를 검수할 때

> ##### 리팩토링의 효용성 - Kent Beck
> * 오늘 일을 오늘 할 수 있어도 내일 일을 내일 할 능력이 없다면 개발자로서 실패하게 된다.
> * 오늘 해야 할 일은 알아도 내일 일은 알 수 없는 것이 당연하다.
> * 그렇지만, 오늘 할 일은 알겠는데 내일 할 일은 모르겠다고 해서, 오늘 일에만 전력을 쏟아버리면 내일은 아무 일도 할 수 없다.
> * 리팩토링을 실시하면 이런 곤경에서 벗어날 수 있다.
> * 리팩토링은 실행 중인 프로그램의 기능을 바꾸는 작업이 아니고 신속한 개발 공정을 가능하게 하면서 가치를 높이는 일이다.
> * 프로그램 수정이 힘든 네 가지 경우
>   1. 코드를 알아보기 힘들 때
>   2. 중복된 로직이 있을 때
>   3. 추가 기능을 넣어야 해서 실행 중인 코드를 변경해야 할 때
>   4. 조건문 구조가 복잡할 때  

<br>

### 팀장에게 어떻게 말을 꺼내나

> ##### 인다이렉션과 리팩토링 - Kent Beck
> * 리팩토링은 많은 객체와 장황한 메소드를 잘게 쪼개는 경향이 있다.
> * 이런 인다이렉션은 양날의 검이다. 한 부분을 둘로 쪼개면 관리할 부분이 늘어난다.
> * 객체간의 의존성이 높아지기 때문에 코드를 알아보기 힘들어질 수도 있다.
> * 인다이렉션은 자제하는 것이 좋으나 장점도 존재한다.
>   1. 여러 곳에서 호출되는 하위 메소드나 하위 클래스가 공유하는 상위 클래스의 메소드 등, 하나의 로직을 여러 곳에서 공유할 수 있다.
>   2. 클래스명과 메소드명을 통해서 의도한 바를 드러낼 수 있고, 내부 코드를 통해 그 의도를 어떻게 구현했는지 보여줄 수 있다.
>   3. 한 객체를 두 위치에서 사용했을 때, 두 위치 중 한 위치에서 수정이 필요할 때 그 객체를 수정하면 두 상황이 모두 변경될 위험이 있다. 따라서 하위클래스를 만들고 변하는 경우에 참조하게 만들자.
>   4. 객체에는 재정의 메시지라는 우수한 메커니즘이 존재해서 조건문을 유연하면서도 분명하게 표현할 수 있다.

<br>

### 리팩토링 관련 문제들

1. 데이터베이스
    * 애플리케이션은 바탕이 되는 데이터베이스 스키마와 강력하게 결합되어 있으며, 스키마를 수정하면 데이터도 이전해야 하는 문제가 있어서 데이터베이스 수정이 어렵다.
    * 이런 문제를 해결하기 위해 객체 모델과 데이터베이스 모델 사이에 별도의 소프트웨어 계층을 두는 방법이 있다.
    * 두 모델에 생긴 변경 사항을 따로 유지할 수 있어서 한 모델을 수정할 대 다른 모델은 수정할 필요 없이 중개 계층만 수정하면 된다.
    * 중개 계층이 생기면 복잡하긴 하나 상당한 유연성이 생긴다.
2. 인터페이스 변경
    * 리팩토링에서 불안한 점은 상당수의 리팩토링이 인터페이스를 수정한다는 것이다.
    * 인터페이스가 사용되는 부분을 찾는 게 불가능하거나 수정할 수 없을 경우에 문제가 생긴다.
    * published 인터페이스를 수정하는 경우, 기존 인터페이스와 새 인터페이스를 모두 그대로 유지시켜야 한다. (메소드명을 변경할 때는 기존 메소드가 새 메소드를 호출하게 수정)
    * 더불어 deprecation 타입을 작성해서 호출자에게 그 코드를 사용하지 말아야 함을 알려야 한다.
3. 리팩토링을 어렵게 하는 설계를 수정하는 일
    * 설계 자체 오류가 있을 때나 설계에 대한 결정이 바뀌었을 때, 혹은 수정하기 힘들 것 같은 민감한 부분일 때도 리팽토릭으로 해결할 수 있다.
    * 프레임워크 선택, 연동 기술 선택 같은 특정 설계적 판단을 배제한 채 리팩토링 공정은 어렵긴 해도 가능하다.
4. 리팩토링하면 안 되는 상황
    * 코드를 처음부터 새로 작성해야 할 때 (코드가 돌아가지 않는다면 완전히 새로 작성하라는 신호이다. 코드는 반드시 대부분 제대로 돌아가는 것이 우선이고, 리팩토링은 나중이다)
    * 납기가 임박했을 때

<br>

### 리팩토링과 설계

* 리팩토링은 설계를 보완하는 특수한 역할을 한다.
* Agile Manifesto 기업의 공동 개발자 Alistair Cockburn "설계를 하면 생각이 아주 빨라지지만 그 생각엔 빈틈이 많다"
* 리팩토링이 사전 설계를 대체할 수 있다는 주장도 존재한다. 리팩토링으로 사전 설계를 대체할 땐 설계를 하면 안 된다. 우선 아이디어를 코딩하고 잘 돌아가게 만든 후 리팩토링으로 다듬어야 한다.
* 리팩토링하지 않을 때는 사전 설계를 정확히 해야 한다는 부담감이 크다. 나중에 설계를 수정하려면 큰 비용이 들기 때문에 사전 설계에 더 많은 시간과 노력을 들여야 한다.
* 리팩토링을 통해 개발자는 수정할 때 따르는 위험성에 다른 각도로 대처할 수 있다.
* 리팩토링을 실시하면 유연성을 낮추지 않고도 더 간결한 설계가 가능해진다.

<br>

### 리팩토링과 성능

* 소프트웨어를 이해하기 쉽게 만들려면 수정할 일이 많은데 그런 수정으로 프로그램이 느려질 수도 있다.
* 소프트웨어는 속도가 느린 것이 용납되지 않으며, 하드웨어 성능이 빨라질수록 그에 맞춰 소프트웨어 속도에 대한 기대나 기준도 더 높아질 뿐이다.
* 리팩토링을 실시하면 분명 소프트웨어는 더 느려지지만, 소프트웨어 성능을 더 간단히 조절할 수 있다.
* 프로그램을 잘 쪼개면 두 가지 측면에서 최적화 방식에 도움이 된다.
    1. 성능 튜닝에 할애할 시간이 생긴다. 코드가 잘 쪼개져 있어서 기능 추가도 빠르다. 그로 인해 더 많은 시간을 성능에만 집중할 수 있다.
    2. 성능을 분석할 때 더 정밀한 분석이 가능해진다.
* 리팩토링하는 동안에는 단기적으로 소프트웨어가 느려지지만, 최적화를 거치면서 튜닝하기가 훨씬 쉬워지고 결과적으로는 소프트웨어 개발이 더 빨라진다.
