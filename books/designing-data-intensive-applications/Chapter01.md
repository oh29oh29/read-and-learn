# 01. 신뢰할 수 있고 확장 가능하며 유지보수하기 쉬운 애플리케이션

오늘날 많은 애플리케이션은 계산 중심(compute-intensive)과는 다르게 데이터 중심(data-intensive)적이다.

- 구동 애플리케이션이나 다른 애플리케이션에서 나중에 다시 데이터를 찾을 수 있게 데이터를 저장 (데이터베이스)
- 읽기 속도 향상을 위해 값비싼 수행 결과를 기억 (캐시)
- 사용자가 키워드로 데이터를 검색하거나 다양한 방법ㄷ으로 필터링할 수 있게 제공 (검색 색인, search index)
- 비동기 처리를 위해 다른 프로세스로 메시지 보내기 (스트림 처리, stream processing)
- 주기적으로 대량의 누적된 데이터를 분석 (일괄 처리, batch processing)

### 데이터 시스템에 대한 생각

데이터 저장과 처리를 위한 여러 새로운 도구는 최근에 만들어졌다.  
새로운 도구들은 다양한 사용 사례(use case)에 최적화됐기 때문에 더 이상 전통적인 분류에 딱 들어맞지 않는다.

점점 더 많은 애플리케이션이 단일 도구로는 더 이상 데이터 처리와 저장 모두를 만족시킬 수 없는 과도하고 광범위한 요구사항을 갖고 있다.  
대신 작업(work)은 단일 도구에서 효율적으로 수행할 수 있는 태스크(task)로 나누고 다양한 도구들은 애플리케이션 코드를 이용해 서로 연결한다.

서비스 제공을 위해 각 도구를 결합할 때 서비스 인터페이스나 애플리케이션 프로그래밍 인터페이스(API)는 보통 클라이언트가 모르게 구현 세부 사항을 숨긴다.  
기본적으로 좀 더 작은 범용 구성 요소들로 새롭고 특수한 목적의 데이터 시스템을 만든다.  
복합 데이터 시스템(composite data system)은 외부 클라이언트가 일관된 결과를 볼 수 있게끔 쓰기에서 캐시를 올바르게 무효화하거나 업데이트하는 등의 특정 보장 기능을 제공할 수 있다.  

이제부터 개발자는 애플리케이션 개발자뿐만 아니라 데이터 시스템 설계자이기도 하다.

### 신뢰성

"무언가 잘못되더라도 지속적으로 올바르게 동작함"을 신뢰성의 의미로 해석할 수 있다.

잘못될 수 있는 일을 결함(fault)이라 부른다.  
결함을 예측하고 대처할 수 있는 시스템을 내결함성(fault-tolerant) 또는 탄력성(resilient)을 지녔다고 말한다.

결함은 장애(failure)와 동일하지 않다.  
일반적으로 결함은 사양에서 벗어난 시스템의 한 구성 요소로 정의되지만, 장애는 사용자에게 필요한 서비스를 제공하지 못하고 시스템 전체가 멈춘 경우다.

#### 하드웨어 결함

시스템 장애율을 줄이기 위한 첫 번째 대응으로 각 하드웨어 구성 요소에 중복(redundancy)을 추가하는 방법이 일반적이다.

#### 소프트웨어 오류

예상하기 어렵고 노드 간 상관관계 때문에 상관관계 없는 하드웨어 결함보다 오히려 시스템 오류를 더욱 많이 유발하는 경향이 있다.

소프트웨어의 체계적 오류 문제는 신속한 해결책이 없다.  
시스템의 가정과 상호작용에 대해 주의 깊게 생각하기, 빈틈없는 테스트, 프로세스 격리, 죽은 프로세스의 재시작 허용, 프러덕션 환경에서 시스템 동작의 측정, 모니터링, 분석하기와 같은 여러 작은 일들이 문제 해결에 도움을 줄 수 있다.  
시스템이 뭔가를 보장하길 기대한다면 수행 중에 이를 지속적으로 확인해 차이가 생기는 경우 경고를 발생시킬 수 있다.

#### 인적 오류

대규모 인터넷 서비스에 대한 한 연구에 따르면 운영자의 설정 오류가 중단의 주요 원인인 반면 하드웨어(서버나 네트워크) 결함은 중단 원인의 10~25% 정도에 그친다.

### 확장성

시스템이 현재 안정적으로 동작한다고 해서 미래에도 안정적으로 동작한다는 보장은 없다.  
성능 처하를 유발하는 흔한 이유 중 하나는 부하 증가다.

#### 부하 기술하기

무엇보다 시스템의 현재 부하를 간결하게 기술해야 한다.  
그래야 부하 성장 질문(부하가 두 배로 되면 어떻게 될까?)을 논의할 수 있다.  
부하는 부하 매개변수(load parameter)라 부르는 몇 개의 숫자로 나타낼 수 있다.  
가장 적합한 부하 매개변수 선택은 시스템 설계에 따라 달라진다.

부하 매개변수로 웹 서버의 초당 요청 수, 데이터베이스의 읽기 대 쓰기 비율, 대화방의 동시 활성 사용자(active user), 캐시 적중률 등이 될 수 있다.

#### 성능 기술하기

대부븐의 요청은 꽤 빠르지만 가끔 꽤 오래 걸리는 특이 값(outlier)이 있다.

보고된 서비스 평균 응답 시간을 살피는 일은 일반적이다.  
하지만 "전형적인" 응답 시간을 알고 싶다면 평균은 그다지 좋은 지표가 아니다.  
얼마나 많은 사용자가 실제로 지연을 경험했는지 알려주지 않기 대문이다.

일반적으로 평균보다는 백분위(percentile)를 사용하는 편이 더 좋다.  
응답 시간 목록을 가지고 가장 빠른 시간부터 제일 느린 시간까지 정렬하면 중간 지점이 중앙값(median)이 된다.

특이 값이 얼마나 좋지 않은지 알아보려면 상위 백분위를 살펴보는 것도 좋다.  
이때 사용하는 백분위는 95분위, 99분위, 99.9분위가 일반적이다.

#### 부하 대응 접근 방식

사람들은 확장성과 관련해 용량 확장(scaling up)과 규모 확장(scaling out)으로 구분해서 말하곤 한다.  
다수의 장비에 부하를 분산하는 아키텍처를 비공유(shared-nothing) 아키텍처라 부른다.

대개 대규모로 동작하는 시스템의 아키텍처는 해당 시스템을 사용하는 애플리케이션에 특화돼 있다.  
범용적이고 모든 상황에 맞는 확장 아키텍처는 없다.  
아키텍처를 결정하는 요소는 읽기의 양, 쓰기의 양, 저장할 데이터의 양, 데이터의 복잡도, 응답 시간 요구사항, 접근 패턴 등이 있다.

### 유지보수성

소프트웨어 비용의 대부분은 초기 개발이 아니라 지속해서 이어지는 유지보수에 들어간다는 사실은 잘 알려져 있다.

유지보수 중 고통을 최소화하고 레거시 소프트웨어를 직접 만들지 않게끔 소프트웨어를 설계할 수 있다.  
그러기 위해 주의를 기울여야 할 소프트웨어 시스템 설계 원칙은 다음 세 가지다.

#### 운용성: 운영의 편리함 만들기

좋은 운용성이란 동일하게 반복되는 태스크를 쉽게 수행하게끔 만들어 운영팀이 고부가가치 활동에 노력을 집중한다는 의미다.

#### 단순성: 복잡도 관리

복잡도는 같은 시스템에서 작업해야 하는 모든사람의 진행을 느리게 하고 나아가 유지보수 비용이 증가한다.  
복잡도를 줄이면 소프트웨어 유지보수성이 크게 향상된다. 따라서 단순성이 구축하려는 시스템의 핵심 목표여야 한다.

시스템을 단순하게 만드는 일이 반드시 기능을 줄인다는 의미는 아니다.  
우발적 복잡도(accidental complexity)를 줄인다는 뜻일 수도 있다.  

우발적 복잡도를 제거하기 위한 최상의 도구는 추상화다.  
좋은 추상화는 깔끔하고 직관적인 외관 아래로 많은 세부 구현을 숨길 수 있다.  
또한 좋은 추상화는 다른 다양한 애플리케이션에서도 사용 가능하다.  

#### 발전성: 변화를 쉡게 만들기

시스템을 쉽게 변경할 수 있게 하라.