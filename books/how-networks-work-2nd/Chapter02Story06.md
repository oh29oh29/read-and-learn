# 06. UDP 프로토콜을 이용한 송∙수신 동작 

### 수정 송신이 필요없는 데이터의 송신은 UDP 가 효율적이다

TCP 프로토콜이 아닌 UDP 프로토콜을 사용하여 송∙수신하는 애플리케이션도 존재한다.  

TCP 동작은 상당히 복잡한데, 이처럼 복잡한 이유는 데이터를 확실하면서도 효율적으로 전달하기 위해서이다.  
데이터를 확실히 전달하려면 도착한 것을 확인하고, 도착하지 않았으면 다시 보내야 한다.

가장 간단히 실현하는 방법은 데이터를 '전부' 보낸 후에 수신측에서 수신 확인 응답을 받는 방법이다.  
만약 도착하지 않은 경우 한 번 더 전부 다시 보내면 되므로 TCP 가 하듯이 어디까지 도착했는지 또는 어디부터 다시 보내야 하는지 등의 복잡한 일을 생각할 필요가 없다.  
하지만 패킷이 한 개만 없어져도 전체를 다시 보내므로 비효율적이다.

하지만 데이터가 한 개의 패킷에 수용할 수 있을 만큼 길이가 짧은 경우에는 오히려 효율적이다.  
패킷이 한 개밖에 없다면 이것이 없어졌는지 생각할 필요가 없지만, 데이터를 전부 다시 보낸다 해도 패킷을 한 개만 보내므로 낭비가 아니다.  
TCP 처럼 복잡한 구조가 필요하지 않기 때문이다.

### 제어용 짧은 데이터

DNS 서버에 대한 조회 등 제어용으로 실행하는 정보 교환은 한 개의 패킷으로 끝나는 경우가 많으므로 UDP 를 사용한다.  
UDP 에는 TCP 와 같은 수신 확인이나 윈도우가 없어서 데이터 송∙수신 전에 제어 정보를 주고받을 필요가 없고, 접속이나 연결 끊기 단계가 없다.  
애플리케이션에서 송신 데이터를 받으면 여기에 UDP 헤더를 부가하고 이것을 IP 에 의뢰하여 송신하기만 한다.

수신도 간단하다.  
IP 헤더에 기록되어 있는 수신처 IP 주소와 송신처 IP 주소, 그리고 UDP 헤더에 기록되어 있는 수신처 포트 번호와 송신처 포트 번호라는 네 항목과 소켓에 기록된 정보를 결합하여 데이터를 건네줄 대상 애플리케이션을 판단하고 여기에 데이터를 건네주기만 한다.  
일단 패킷을 보낸 후 TCP 처럼 보낸 패킷의 상태를 감시하지 않으므로 오류가 발생해도 프로토콜 스택은 신경쓰지 않지만 문제가 없다.

### 음성 및 동영상 데이터

음성이나 영상의 데이터를 보낼 때도 UDP 를 사용한다.  
음성이나 영상에는 데이터가 다소 없어도 치명적인 문제가 되지 않는 성질이 있다.  
이와 같이 다시 보낼 필요가 없거나 다시 보내도 쓸모가 없으면 단순히 UDP 로 데이터를 보내는 쪽이 더 효율적이다.