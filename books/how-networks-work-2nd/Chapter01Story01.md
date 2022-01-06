# 01. HTTP 리퀘스트 메시지를 작성한다

### 탐험 여행은 URL 입력부터 시작한다

URL 형식  

- HTTP 프로토콜로 웹 서버에 엑세스하는 경우
  
```
프로토콜://사용자명(생략가능):패스워드(생략가능)@웹 서버의 도메인명:포트 번호(생략가능)/파일의 경로명
http://user:password@www.oh29oh29.com:80/dir/file1.html
```
        

### 브라우저는 먼저 URL 을 해독한다

브라우저가 처음 하는 일은 웹 서버에 보내는 리퀘스트의 메시지를 작성하기 위해 이 URL 을 해독하는 것이다.

```
[ http: ] URL 의 맨 앞에는 데이터 출처에 엑세스 하는 방법, 즉 프로토콜을 기록  
[ // ] 나중에 이어지는 문자열이 서버의 이름임을 나타냄  
[ 웹 서버명 ]  
[ /디렉토리명/.../파일명 ] 데이터 출처(파일)의 경로명을 나타내며 생략 가능.
```

### 파일명을 생략한 경우

```
http://www.lab.cyber.co.kr/dir/
```

끝이 / 로 끝난 것은 파일명을 쓰지 않고 생략한다는 것이다.  
하지만 파일명을 쓰지 않으면 어느 파일에 엑세스해야 할지 모른다. 그래서 이를 ㅁ대비해 미리 서버측에 설정해둔다.

이 설정은 서버에 따라 다르지만 대부분의 서버가 'index.html' 또는 'default.html' 이라는 파일명을 설정해둔다.

```
http://www.lab.cyber.co.kr/dir
```

이렇게 끝의 / 까지 생략도 할 수 있다.  
경로명이 아무 것도 없는 경우에는 루트 디렉토리의 아래에 있는 미리 설정된 파일명의 파일에 엑세스한다.

### HTTP 의 기본 개념

![HTTP 의 기본 개념](images/IMG_01_01.png)

#### 메서드 의미

```
GET - URI 로 지정한 정보를 도출
POST - 클라이언트에서 서버로 데이터를 송신
HEAD - HTTP 메시지 헤더 정보만 도출
OPTIONS - 통신 옵션을 통지하거나 조사
PUT - URI 로 지정한 서버의 파일을 치환 (없는 경우 새로 파일을 작성)
DELETE - URI 로 지정한 서버의 파일을 삭제 
TRACE - 서버측에서 받은 리퀘스트 라인과 헤더를 그대로 클라이언트에 반송
CONNECT - 암호화한 메시지를 프록시로 전송할 때 사용
```