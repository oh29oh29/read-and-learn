# 12. toString 을 항상 재정의하라

### toString 의 일반 규약

- 간결하면서 사람이 읽기 쉬운 형태의 유익한 정보를 반환한다.
- 모든 하위 클래스에서 이 메서드를 재정의하라

toString 은 그 객체가 가진 주요 정보를 모두 반환하는 게 좋다.  
toString 을 재정의한 클래스는 디버깅하기 쉽게 해준다. 