studynote#1 : webSocket
=======================
webSocket 은 웹 페이지와 서버간의 실시간 통신을 위해 만들어 졌으며, webSocket 이전에도 실시간 통신을 구현하기 위한 여러가지 시도가 있었다.  

## 기존의 양방향 통신 방법  
HTTP request를 계속 보내는 방식(트릭)으로 실시간으로 작동하는 것처럼 하는 기술을 사용했다.  
### polling  
![polling](./img/polling.png)  
클라이언트가 평범한 http request를 서버로 계속 날려서 이벤트 내용을 전달받는 방식이다.  
가장 쉬운방법이지만 클라이언트가 계속적으로 request를 날리기때문에 클라이언트가 많아지면 서버의 부담이 급증하게 된다.  
단점  
* http request connection을 맺고 끊는것은 부담이 많은 방식이다.   
* 클라이언트에서 실시간정도의 빠른 응답을 기대하기도 어렵다.  

### long polling  
![longPolling](./img/longPolling.png)  
클라이언트에서 서버로 일단 http request를 날린다.  
이상태로 계속 기다리다가 서버에서 해당 클라이언트로 전달할 이벤트가 있다면 그순간 response 메시지를 전달하면서 연결이 종료된다.  
클라이언트에서는 곧바로 다시 http request를 날려서 서버의 다음 이벤트를 기다리게 되는 방식이다.   
단점
* 일반 polling 방식보다는 서버의 부담이 줄겠지만 클라이언트로 보내는 이벤트들의 시간간격이 좁다면 polling 과 별 차이가 없게 된다 
* 다수의 클라이언트에게 동시에 이벤트가 발생될 경우에는 곧바로 다수의 클라이언트가 서버로 접속을 시도하면서 서버의 부담이 급증하게 된다.

### streaming : SSE (Server Sent Event)  
![streaming](./img/streaming.png)   
서버와 처음 한 번만 연결하면 서버가 클라이언트에 지속적으로 데이터를 보내준다.
서버에서 클라이언트로 이벤트를 전달할때 해당 요청을 끊지 않고 필요한 메시지만 보내기를(flush) 반복하는 방식이다.  
즉, 서버에서 클라이언트로 업데이트를 스트리밍 할 수 있습니다
long polling에 비해 서버에서 메시지를 보내고도 다시 http request 연결을 하지 않아도 되어 부담이 경감된다.
단점
* 서버에서 클라이어트로의 단방향 통신이다.
활용
* 주식 차트 갱신, 게시물 갱신 등 양방향 통신이 필요없는 경우에 사용한다.

## What is webSocket?
webSocket은 웹페이지와 서버간에 실시간 통신을 위해 만들어진 구조이다.  
WebSocket은 서버와 클라이언트 간에 socket connection을 유지해서 언제든 양방향 통신
(전이중 통신) 또는 데이터 전송이 가능하도록 하는 기술이며   
2009년 구글 크롬을 시작으로 여러 웹브라우저에서 이 기술을 적용하여서 현재   
Real-time web application 구현을 위해 널리 사용되고 있다.  

### Real-time web application
서버 쪽 또는 클라이언트 쪽 데이터가 실시간으로 업데이트 되는 웹 어플리케이션을 의미한다.  

### use HTTP
webSocket이 일반 TCP Socket 과 다른 점은 최초 접속이 http request 를 통해 이루어 진다는 것이다.  
http request를 그대로 사용하기 때문에 기존의 80, 443 포트로 접속을 하므로 추가로 방화벽을 열지 않고도 양방향 통신이 가능하고,   
http 규격인 CORS적용이나 인증등의 과정을 기존과 동일하게 가저갈 수 있는것이 장점이다.  

## issue : 웹 소켓 미지원 브라우저  
webSocket 사용 시 브라우저에 따른 어려움이 발생할 수 있다.  
인터넷 익스플로러 구버전 사용자들은 WebSocket으로 작성된 웹 페이지를 볼 수가 없기 때문에 웹 서비스를 제공하는 사업자 입장에서는 치명적일 수 있다.
인터넷 익스플로어 같은 경우에는 10 버전부터 webSocket이 지원된다.  

## solution : Socket.io, Socket.js  
위의 issue를 해결하기 위해 나온 기술들이 몇가지 있는데 원리는 간단하다.  
웹페이지가 열리는 브라우저가 webSocket을 지원하면 일반 webSocket 방식으로 동작하고 지원하지 않는 브라우저라면 위에서 설명한 일반 http 스팩을 이용해서 실시간통신을 흉내낼수 있는 방식으로 통신을 하게 해주는 것이다.

### Socket.io  
http://socket.io  
node.js 기반으로 만들어진 기술로 자체 스팩으로 만들어진 socket.io 서버를 만들고 socket.io 클라이언트와 브라우저에 구애받지 않고 실시간 통신이 가능해진다.  socket.io는 node.js 기반이기때문에 모든 코드가 javascript로 작성되어 있다.  
서버, 클라이언트 모두 javascript 기반으로 개발하는 것이 기본이다.  
  
### Socket.js  
http://sockjs.org  
springframework에서 WebSocket을 지원한다.  
스프링 메뉴얼에 webSocket 부분을 보면 위와 같은 브라우저 문제를 해결하기 위한 방법으로 SockJS를 솔루션으로 제시한다.  
역시 자체 스팩으로 webScoket 미지원 브라우저를 관리한다.  
서버개발시 스프링 설정에서 일반 webSocket 으로 통신할지 SockJS 호환으로 통신할지 결정할 수 있다.  
클라이언트쪽은SockJS client를 통해 서버와 통신한다.

## example : webSocket 의 활용 예시
* 페이스북 같은 SNS 어플리케이션
* LOL 같은 멀티플레이어 게임들
* 구글 Doc 같이 여러 명이 동시 접속해서 수정할 수 있는 Tool
* 클릭 동향 분석 데이터 어플 
* 증권 거래 정보 사이트 및 어플
* 스포츠 업데이트 정보 사이트 및 어플
* 화상 채팅 어플
* 위치 기반 어플
