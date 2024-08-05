# 3-Way Handshake
TCP는 장치들 사이에 논리적인 접속을 성립(establish)하기 위하여 3-way handshake를 사용한다.

TCP 3-Way Handshake는 TCP/IP프로토콜을 이용해서 통신을 하는 응용프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정을 의미한다.

신뢰성을 위해 3번의 핸드쉐이킹을 거쳐 연결을 맺는 것
Client > Server : TCP SYN
Server > Client : TCP SYN ACK
Client > Server : TCP ACK


__[1단계]__ 클라이언트 --- SYN ---> 서버
- 클라이언트가 서버에게 연결을 요청하는 SYN 세그먼트를 보낸다.

    - 세그먼트 헤더의 ```SYN 비트는 1```로 설정
    - 클라이언트 ```최초의 순서번호(client_isn)```를 무작위로 설정
    - SYN 세그먼트를 전송한 후 SYNSENT 상태로 서버의 ACK Segment를 기다린다.
- 서버는 클라이언트로부터 ```SYN 세그먼트```를 수신한다.

__[2단계]__ 클라이언트 <--- (ACK + SYN) --- 서버
- 서버가 클라이언트의 ```SYN 세그먼트```에 대한 ```ACK 세그먼트```를 전송

    - ```확인응답 필드```는 ```client_isn + 1```로 설정
    - 서버가 보내는 SYN 세그먼트의 ```최초의 순서번호(sever_isn)```도 무작위로 설정한다.
- 서버가 클라이언트에게 연결을 요청하는 ```SYN 세그먼트``` 전송

    - 1로 설정된 SYN 비트
    - SYN/ACK 세그먼트를 전송하고 ```SYN RCVD```의 상태로 클라이언트의 ACK를 기다린다.

- 연결 승인 세그먼트는 ```SYNACK 세그먼트```라고 부른다.


__[3단계]__ : 클라이언트 --- (ACK) ---> 서버
- 클라이언트는 서버의 ```SYN 세그먼트```에 대한 ```ACK 세그먼트```를 전송

    - 연결이 설정되었기 때문에 SYN 비트는 0으로 설정
    - 클라이언트는 ```TCP 세그먼트 헤더```의 ```확인응답 필드 안에 server_isn+1``` 값을 넣는 것으로 서버가 연결 승인을 확인할 수 있게 한다.
- 클라이언트는 ```SYNACK 세그먼트```를 수신하면 연결에 ```버퍼와 변수```들을 할당한다.
- 3단계에서는 클라이언트에서 서버로 데이터를 전송할 수 있다.

![](https://velog.velcdn.com/images/wxxhyeong/post/b969b346-adf5-42a4-8655-54ee3257212d/image.png)


# 2-Way Handshake를 하지 않는 이유
### 2-Way-Handshake
- 2-Way Handshake에서는 하나의 SYN 요청, 하나의 ACK 응답만 존재한다.

#### 정상적인 2-Way Handshake 과정
![](https://velog.velcdn.com/images/wxxhyeong/post/60ec9ad1-a35c-4963-8650-ab0705d2f51b/image.png)

__[1단계]__ : 클라이언트 --- (SYN) ---> 서버
- 클라이언트가 서버에게 연결을 요청한다.

    - SYN 패킷을 전송한다.
- 서버는 SYN 패킷을 받고 ESTAB 상태가 된다.

__[2단계]__ : 클라이언트 <--- (SYN) --- 서버
- 서버는 연결 수락 및 응답 패킷을 보낸다.

    - ACK 패킷을 전송
- 클라이언트는 ACK 패킷을 받고 ESTAB 상태가 된다.

### 문제점
- __ISN 동기화__가 이루어지지 않는다.

    - 서버가 전송한 Segment의 순서를 구분할 수 없다.

        - 패킷 손실이나 지연을 탐지할 수 없다.
        - 작업 순서가 달라져 오류가 발생한다.
        - 작업의 중복 수행이 발생할 수 있다.
- __Half Open Connection"__ 상황이 발생할 수 있다.

    - 한 쪽 Host마나 연결된 상태를 의미
    - 연결된 Host는 계속 자원을 낭비
    - SYN Flooding에 취약

#### 문제 상황 1
![](https://velog.velcdn.com/images/wxxhyeong/post/bd8c18d9-b9e1-49be-b256-12f19df49048/image.png)

#### 문제 상황 2
![](https://velog.velcdn.com/images/wxxhyeong/post/9d79fcf7-7e23-46a4-9ae9-e918d0993439/image.png)


# 4-Way Handshake
3-way handshake는 TCP의 연결을 초기화할 때 사용한다면, 4-way handshake는 세션을 종료하기 위해 수행한다.

![](https://velog.velcdn.com/images/wxxhyeong/post/bc314ced-76a8-4a0a-8f55-89c13c94537a/image.png)



## 두 호스트가 동시에 연결을 시도하면 어떻게 될까?
이것을 __“Simultaneous Open”__ 이라고 한다.

TCP 는 두 개의 파이프를 사용하는 양방향 전이중 통신에 해당한다.

연결이 된 후에는 자연스럽게 양방향 데이터 전송이 가능하다.
![](https://velog.velcdn.com/images/wxxhyeong/post/7bf808c3-3bee-4efa-b63b-1fa20eb63739/image.png)

1. 각자 서로 SYN Segment를 보낸다. - SYN-SENT
2. SYN Segment를 받으면 ACK Segment를 보낸다. - SYN-RCVD
3. ACK Segment가 도착하면 각자 Established 된다.
- 3-Way Handshake 보다 빠르다.

## SYN Flooding
__SYN Flooding__은 네트워크 보안 공격 중 하나로, 공격자가 대상 서버에 대량의 TCP SYN 요청 패킷을 보내서 서버의 리소스를 고갈시키는 공격이다.

![](https://velog.velcdn.com/images/wxxhyeong/post/4e9341de-5315-444c-b89c-2778c58ad4e7/image.png)

__[1단계]__ : 공격자 --- (SYN) ---> 서버
- 대량의 SYN 패킷을 전송한다.
- 대량 연결 요청 자체에서 서버에게 부담을 준다.

    - 대량의 SYN 패킷에 자원을 할당함으로써 서버의 리소스가 모두 소진되어 응답하지 못하는 상태가 된다.
      = __서비스 거부(DoS, Denial of Service) 상태__

__[2단계]__ : 공격자 <--- (SYN+ACK) --- 서버
- ```SYN+ACK```패킷을 전송하고 ```ACK```를 받을 때까지 대기한다.

    - 서버는 ```half-open``` 상태가 된다.
- 하지만, 공격자는 ACK를 전송하지 않고 서버를 계속 기다리게 한다.

    - ```half-open``` 연결이 계속 쌓이고, 서버의 연결 테이블이 고갈되어 __서비스 고갈 상태__가 된다.


### 방어 방법
1. 대기 큐(백로그 큐) 크기 늘리기
- 저장공간을 늘려 수용할 수 있는 양을 늘리는 방법.
- 결국 가득차게 되어 __근본적인 해결방법이 될 수 없다.__

2. SYN Cookie
- 3-way handshake와 유사하다.
- SYN Flooding에 의해 SYN backlog가 가득 찼을 때만 쿠키를 생성한다.
- SYN Cookie를 사용한다는 점에서 다르다.

#### SYN Cookie 작동 방식
__[1단계]__ : 클라이언트 --- (SYN) ---> 서버
- 클라이언트가 서버에게 tcp 연결 요청을 한다.
- 서버는 클라이언트의 SYN 세그먼트의 출발지와 목적지 IP 주소들과 포트 번호들과 해시함수로 초기 ```TCP 순서번호(Sequence number)```를 만든다. 이 순서번호가 ```SYN Cookie```가 된다.

__[2단계]__ : 클라이언트 <--- (ACK + SYN) --- 서버
- 서버는 ```SYN+ACK 패킷의 ISN```에 쿠키 값을 넣어서 전송한다.
- 서버는 클라이언트로 부터 받은 SYN 패킷과 연결 정보를 저장할 필요가 없다.
  어차피 나중에 ACK로 받을 때 다시 해시함수로 쿠키 값을 계산하고 확인응답 필드와 비교하면 되기 때문

__[3단계]__ : 클라이언트 --- (ACK) ---> 서버
- 클라이언트는 ```ACK 응답 패킷```을 보낸다.
- 서버는 ```ACK 응답 패킷```의 확인응답 번호를 확인한다.
- ```확인 응답 번호```는 이전 ```SYNACK```의 순서번호에 1을 더한 값과 같다.
- 같으면 올바른 클라이언트라고 판단하고 연결을 만든다.

## 0-RTT 기법
