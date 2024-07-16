## HTTP/1.0

- HTTP 헤더 개념을 통해, 요청/ 응답 모두 Meta data 전송을 허용하여 프로토콜을 유연하고 확장 가능하도록 함
- 버전 정보와 요청 method가 함께 전송되기 시작
- Response 시작 부분에 상태 코드 라인이 추가되어 브라우저 요청의 성공/ 실패를 파악 가능

    - 해당 결과에 대한 로컬 캐시 갱신 등의 사용 가능
- Content-Type 도입으로 HTML 이외의 문서 전송 기능이 가능해짐

```HTTP
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
A page with an image
  <IMG SRC="/myimage.gif">
</HTML>
```
##### HTTP/1.0 한계
커넥션 하나당 요청 하나와 응답 하나만 처리 가능하여, 매우 비효율적이며 서버 부하도 문제

## HTTP/1.1 - 표준 프로토콜
HTTP의 첫 번째 표준호 버전인 HTTP/1.1은 HTTP/1.0 이후 불과 몇 달 뒤인 1997년 초에 발표됨

HTTP/1.1은 모호함을 명확하게 하고 많은 개선 사항들을 도입

- Persistent Connection

    - 지정한 timeout(Keep-Alive) 동안에 커넥션 재사용 가능하게 하여, 기존 연결에 대해 Handshaking 생략

- Pipelining 추가

    - 앞 요청의 응답을 기다리지 않고 순차적인 여러 요청을 연속적으로 보내고 그 순서에 맞춰 응답을 받는 방식이 등장
    - 순차적으로 하나씩 요청/ 응답이 처리되는 기존 방식을 개선
    - 하나의 커넥션에 여러개의 요청이 들어 있을 뿐, 동시에 여러 개의 요청을 처리해 응답으로 보내주는 것은 아니다.(multiplexing 되지는 않음)

- ```Host``` 헤더 덕분에, 동일 IP 주소에 다른 도메인을 호스트하는 기능이 서버 배치가 가능해졌습니다.

##### 호스트 헤더
```HTTP
Host: example.com
```
- HTTP 요청의 일부
- 클라이언트가 웹 서버에게 요청을 보낼 때 해당 요청이 어떤 도메인의 리소스를 타겟으로 하는지를 알려주는 역할
- 이를 통해 하나의 IP 주소에서 여러 개의 도메인 이름을 가진 웹 사이트를 동시에 호스팅 가능


    - host를 통해 가상 사이트 식별

#### HTTP/1.1 한계: High Latency
- Head Of Line Blocking(HOL)

    - 순서대로 처리(요청/응답)하는 TCP의 특성으로 ,첫 번째 처리가 완료되기까지 다른 요청들이 모두 대기하게 되는 문제
    - 결국 앞 요청의 응답이 너무 오래 걸리면, 뒤 요청은 Blocking 되어버림

- RTT (Round Trip Time)

    - 하나의 Connection에 하나의 요청을 처리하는 특성 상, 3 way handshake 반복 등 latency가 증가하게 됨

- Fat message Headers

    - HTTP/1.1 헤더에는 많은 Meta data가 있음
    - 매 요청 마다 중복된 Header 값을 전송하게 되며 (별도의 domain sharding을 하지 않았을 경우), 또한 해당 domain에 설정된 cookie 정보도 매 요청마다 헤더에 포함되어 전송함
    - 전송하려는 값보다 헤더 값이 더 큰 경우도 자주 발생

3-Way Handshake의 반복 감소와 여전히 존재하는 문제
비록 HTTP/1.1이 3-way handshake의 반복을 줄였지만, 여전히 지연 시간의 다른 요소들이 존재합니다:

Initial Connection Setup: 첫 연결 설정 시에는 여전히 3-way handshake가 필요합니다.
RTT의 영향: 연결을 설정하는 데 걸리는 기본적인 RTT는 여전히 존재합니다.
HOL 블로킹: 앞서 언급한 Head-of-Line 블로킹 문제가 HTTP/1.1에서도 존재합니다.

__결론__
HTTP/1.1의 연결 유지 기능(Persistent Connection)은 3-way handshake의 빈도를 줄여서 지연 시간을 줄이는 데 기여합니다. 그러나 네트워크 지연 시간(Latency)는 여러 요소에 의해 영향을 받으며, 초기 연결 설정 시의 RTT와 요청 처리 지연 등은 여전히 존재합니다. HTTP/2와 같은 새로운 프로토콜들은 이러한 문제들을 해결하기 위해 멀티플렉싱과 헤더 압축 등을 도입하여 네트워크 성능을 더욱 향상시킵니다.

## HTTP/2
- HTTP/2.0은 다중화를 지원하여 단일 연결을 통해 동시에 여러 요청/응답을 보낼 수 있습니다. 이렇게 하면 HOL 차단 문제가 제거되고 전체 처리량이 향상됩니다.
  ![](https://velog.velcdn.com/images/wxxhyeong/post/534fe762-9585-4992-b10b-f27b46721867/image.png)

- 서버 푸시를 사용하여 명시적인 요청을 기다리지 않고 서버가 사전에 리소를 클라이언트에 보내 대기 시간을 줄인다.
  ![](https://velog.velcdn.com/images/wxxhyeong/post/5bb8b963-5cb6-43ed-89ba-324439ec2baa/image.png)

- HTTP/2.0은 헤더 압축을 사용하여 요청 및 응답 헤더의 크기를 줄여 대역폭 활용도를 높였다.

    - 추가적인 Binary Encoding


        - Binary Framing 계층을 추가해서 보내는 메시지를 프레임(frame)이라는 단위로 분할하며 추가적으로 바이너리로 인코딩을 한다.

        - 바이너리 형식 사용으로 파싱 속도 및 전송 속도가 빠르고 오류 발생 가능성이 낮아짐

![](https://velog.velcdn.com/images/wxxhyeong/post/4621357c-e2bf-4bf5-a2e6-6a32e8ee9aeb/image.png)


## HTTP/3.0
- 등장 배경 : HTTP2.0 으로 성능이 향상되었지만, TCP 기반 위에서 동작하기에, 핸드쉐이크 과정에서의 지연시간과 패킷이 유실되거나 오류가 있을 때 패킷을 재전송하는 HOL이 발생한다. TCP로 인터넷 통신을 하는 것이 문제인 것이다.

- 프로토콜 변경 : HTTP/1.1 HTTP/2.0의 개념을 유지하지만, 전송 레이어에 TCP 가 아닌 UDP기반인 QUIC(Quick UDP Internet Connections) 프로토콜을 사용한다.
- UDP(User Datagram Protocol)은 데이터그램 방식을 사용하는 프로토콜. 패킷의 목적지만 정해지면 중간 경로는 신경쓰지 않는다.
- 장점

    - 연결 시 레이턴시 감소 (빠른 연결)

        - 암호화와 핸드쉐이크를 동시에 수행해, HTTP/2.0에 비교해 속도 대폭 향상됨(0RTT)
    - HOLB (Head of Line Blocking) 해결

        - __향상된 멀티플렉싱__: 스트림 간 독립성을 보장하여 하나의 스트림에서 발생한 패킷 손실이 다른 스트림에 영향을 미치지 않습니다. 이는 HOL 블로킹 문제를 해결하고, 전반적인 성능을 향상시킵니다.
    - 보안 강화
        - QUIC 프로토콜 자체에 TLS 1.3이 포함되어 있어, 별도의 보안 설정 없이도 강력한 보안을 제공합니다.
    - 핸드쉐이크에 대한 QUIC의 새로운 접근 방식으로 기본적인 암호화를 제공하기 때문에 공격을 완화하는 데 도움됨