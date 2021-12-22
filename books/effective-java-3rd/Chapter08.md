# 08. finalizer 와 cleaner 사용을 피하라

자바에는 finalizer 와 cleaner 라는 두 가지 객체 소멸자가 존재한다.  

### finalizer
예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요하다.

### cleaner
finalizer 보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.  

finalizer 와 cleaner 는 즉시 수행된다는 보장이 없다. 객체에 접근할 수 없게 된 후 finalizer 나 cleaner 가 실행되기까지 얼마나 걸릴지 알 수 없다.