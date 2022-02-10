# 04. 캐시 서버를 이용한 서버의 부하 분산 

### 캐시 서버의 이용

여러 대의 웹 서버를 설치하는 것이 아니라 다른 방법으로 부하 분산을 하는 방법이 있다.  
데이터베이스 서버와 웹 서버 같은 역할에 따라 서버를 나누는 방법으로, 이러한 역할별 분산 처리 방법 중의 하나가 **캐시 서버**를 사용하는 방법이다.

캐시 서버는 프록시라는 구조를 사용하여 데이터를 캐시에 저장하는 서버이다.  
프록시는 웹 서버와 클라이언트 사이에서 웹 서버에 대한 액세스 동작을 중개하는 역할을 한다.  
액세스 동작을 중개할 때 웹 서버에서 받은 데이터를 디스크에 저장해 두고 웹 서버를 대신하여 데이터를 클라이언트에 반송하는 기능을 가지고 있다.  
이것을 캐시라고 부르며, 캐시 서버는 이 기능을 이용한다.

### 캐시 서버는 갱신일로 콘텐츠를 관리한다

캐시 서버를 사용할 때는 부하 분산 장치와 마찬가지로 캐시 서버를 웹 서버 대신 DNS 서버에 등록한다.

#### 캐시 서버에 데이터가 없는 경우

![캐시 서버에 데이터가 없는 경우](images/IMG_05_04_01.png)

[1] 클라이언트에서 캐시 서버에 보낸 요청
```http request
GET /dir1/sample1.html HTTP/1.1
...
Host: www.oh29oh29.co.kr
Connection: Keep-Alive
```

[2] 캐시 서버에서 웹 서버에 전송한 요청  
- 캐시 서버를 경유한 것을 나타내는 헤더 필드를 추가하고 URI 에서 전송 대상을 판단하여 전송
- 헤더 필드는 그다지 중요하지 않고 캐시 서버의 설정에 따라 붙이지 않는 경우도 존재함
```text
GET /dir1/sample1.html HTTP/1.1
...
Host: www.oh29oh29.co.kr
Connection: Keep-Alive
Via: 1.1 proxy.oh29oh29.co.kr
```

[3] 웹 서버에서 캐시 서버에 전송한 응답
- If-Modified-Since 를 추가하지 않는 경우(캐시에 데이터가 없는 경우) 또는 웹 서버에서 변경된 경우에는 데이터가 그대로 응답
```text
HTTP/1.1 200 OK
...
Connection: close
Content-Type: text/html

<html>
<head> ...
```

[5] 캐시 서버에서 클라이언트에 전송한 응답
- 캐시 서버를 경유한 것을 나타내는 Via 헤더가 붙은 것 이외에는 보통의 응답 메시지와 동일
```text
HTTP/1.1 200 OK
...
Connection: close
Content-Type: text/html
Via: 1.1 proxy.oh29oh29.co.kr

<html>
<head> ...
```

#### 캐시 서버에 데이터가 있는 경우

![캐시 서버에 데이터가 있는 경우](images/IMG_05_04_02.png)

[2] 캐시 서버에서 웹 서버에 전송한 요청
- 캐시에 저장한 일시 이후 데이터가 변경되었는지 조사하는 헤더 필드가 부가되어 웹 서버에 전송
```text
GET /dir1/sample1.html HTTP/1.1
...
Host: www.oh29oh29.co.kr
Connection: Keep-Alive
If-Modified-Since: Wed, 21 Sep 2021 05:29:00 GMT
Via: 1.1 proxy.oh29oh29.co.kr
```

[3] 웹 서버에서 캐시 서버에 전송한 응답
- If-Modified-Since 의 일시 이후 변경이 없었던 경우에는 웹 페이지의 데이터가 아니라 변경이 없다는 의미의 메시지를 응답
```text
HTTP/1.1 304 Not Modified
...
Connection: close
```