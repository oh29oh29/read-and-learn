# 10. 활동성을 최대로 높이기

### 10.1 데드락

`식사하는 철학자 dining philosophers` 문제로 유명한 데드락

쓰레드 하나가 특정 락을 놓지 않고 계속 잡고 있으면 그 락을 확보해야 하는 다른 쓰레드는 락이 풀리기를 영원히 기다릴 수 밖에 없음

쓰레드 A가 락 `L`을 확보한 상태에서 두 번째 락 `M`을 확보하려고 대기하고 있고, 쓰레드 B가 락 `M`을 확보한 상태에서 두 번째 락 `L`을 확보하려고 대기하고 있다면,
양쪽 쓰레드 A와 B는 서로가 락을 풀기를 영원히 기다림

데드락의 경우는 여러가지 존재함
- 락 순서에 의한 데드락
- 동적인 락 순서에 의한 데드락
- 객체 간의 데드락
- 리소스 데드락
- 

### 10.2 데드락 방지 및 원인 추적

#### 락의 시간 제한

#### 쓰레드 덤프를 활용한 데드락 분석