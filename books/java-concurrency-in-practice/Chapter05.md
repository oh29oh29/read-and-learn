# 05. 구성 단위

### 5.2 병렬 컬렉션

Synchronized collection 클래스는 collection의 내부 변수에 접근하는 통로를 일련화 -> thread safe  
하지만 여러 thread가 동시에 synchronized collection을 사용하려고 하면 동시 사용성에서 손해

parallel collection은 여러 thread에서 동시에 사용할 수 있도록 설계됨  
java 5.0에는 hash기반의 `HashMap`을 대치하면서 병렬성을 확보한 `ConcurrentHashMap` 클래스가 포함되어 있음

추가로, `Queue`와 `BlockingQueue` 인터페이스가 추가됨  
`Queue` 인터페이스를 구현한 `ConcurrentLinkedQueue`, `PriorityQueue`도 있음

`BlockingQueue` 구현체들은 큐에 항목을 추가하거나 뽑아낼 때 상황에 따라 대기할 수 있도록 구현되어 있음  
큐가 비어 있을때, 큐에서 항목을 뽑아내는 연산은 새로운 항목이 추가될 때까지 대기  
큐가 꽉 차 있을때, 큐에서 항목을 추가하는 연산은 큐에 빈 자리가 생길 때까지 대기

`BlockingQueue` 클래스는 producer-consumer 패턴을 구현할 때 굉장히 편리함


