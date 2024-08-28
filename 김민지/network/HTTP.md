# HTTP

<br/>

HTTP(Hypertext Transfer Protocol)은 텍스트 기반의 통신 규약으로 **인터넷에서 데이터를 주고받을수 있는 프로토콜**입니다.

클라이언트-서버 구조를 따르며 TCP/IP위에서 작동합니다.

<br/>

### **클라이언트 - 서버 구조**

<img width="500px" src="https://private-user-images.githubusercontent.com/74135929/302174371-05aba080-4045-4a4c-9c51-edaec5011896.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3MzEyNjEsIm5iZiI6MTcyNDczMDk2MSwicGF0aCI6Ii83NDEzNTkyOS8zMDIxNzQzNzEtMDVhYmEwODAtNDA0NS00YTRjLTljNTEtZWRhZWM1MDExODk2LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDAzNTYwMVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTZiYTExNDRiNGVhNzk1MGQxMzNjOTEyNDY2MmY3NmI0NGJmODM2ZDA0NWE4MzU3MTRkNzdmMjQ3YWNkZWFmMWMmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.iKM7cGAYBl9SdrDcMEESVfMiWtuOaPx-nB-QZrUjWbI">

클라이언트(e.g 브라우저)가 HTTP메시지를 통해 서버에 요청하면 서버가 요청에 대한 결과를 만들어 클라이언트로 응답하는 구조를 의미합니다..

- 클라이언트와 서버는 개별적인 메시지 교환에 의해 통신
- 요청(request) : 클라이언트에 의해 전송되는 메시지
- 응답(response) : 요청에 대해 서버에서 응답으로 전송되는 메시지
<br/>

### TCP/IP
TCP/IP는 OSI 7 Layers에서 Layer3, Layer4를 다루는 프로토콜입니다.

TCP/IP를 사용하겠다는 것은 IP주소 체계를 따르고 TCP의 특성을 이용해 신뢰성을 확보하겠다는것을 의미합니다. ( [OSI Layers 참고](https://github.com/jmxx219/CS-Study/blob/main/network/OSI%207%EA%B3%84%EC%B8%B5.md), [3-Way Handshake참고](https://github.com/jmxx219/CS-Study/blob/main/network/3-way%20handshake.md), [4-Way Handshake참고](https://github.com/jmxx219/CS-Study/blob/main/network/4-way%20handshake.md) )

<br/>

### **HTTP의 흐름(1.1버전기준)**
>HTTP/1.1에서는 Persistent Connection을 기본으로 사용하기 때문에, 한 번 TCP 연결을 열면 여러 요청을 처리할 때까지 연결을 유지함. 즉, 여러 요청마다 새로운 TCP 연결을 열지 않아도 되는 것임.

1. **TCP 연결 열기**
    1. 클라이언트는 새 연결 열기
   2. 기존 연결 재사용
   3. 서버에 대한 여러 TCP연결을 열 수 있음

<br/>

2. **HTTP 메시지 전송 (요청)**

    ```jsx
    GET / HTTP/1.1
    Host: developer.mozilla.org
    Accept-Language: fr
    ```

   요청의 구성

    - Method : `GET`
        - 클라이언트가 수행하고자 하는 동작
    - Path : `/`
        - 가져오려는 리소스의 경로
    - Version of the protocal : `HTTP/1.1`
        - HTTP 프로토콜의 버전
    - Headers

      `Host: developer.mozilla.org
      Accept-Language: fr`

        - 서버에 대한 추가 정보 전달하는 선택적 헤더들

<br/>

3. **서버에 응답**

    ```jsx
    HTTP/1.1 200 OK
    Date: Sat, 09 Oct 2010 14:28:02 GMT
    Server: Apache
    Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
    ETag: "51142bc1-7449-479b075b2891b"
    Accept-Ranges: bytes
    Content-Length: 29769
    Content-Type: text/html
    
    <!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
    ```

   응답의 구성

    - Version of the protocol : `HTTP/1.1`
        - HTTP 프로토콜의 버전
    - Status code : `200`
        - 요청의 성공 여부과 그 이유를 나타내는 상태코드
    - Status message : `OK`
        - 상태코드의 짧은 설명을 나타내는 상태 메시지
    - Headers : `Date: 부터 끝까지`

<br/>
<br/>

## HTTPS
HTTPS(Hypertext Transfer Protocol Secure)은 서버에서 브라우저로 전송되는 정보가 암호화되지 않는 HTTP의 문제점을 SSL(Secure Sockets Layer)을 사용한 암호화한 프로토콜이다.

**HTTPS : HTTP 프로토콜의 보안버전**

<img width="500px" src="https://github.com/jmxx219/CS-Study/assets/74135929/38fe0400-2c5a-4bfb-82e4-ca452249ecc0">

- HTTPS는  HTTP(응용계층 프로토콜)와 TCP(전송 계층 프로토콜) 사이에 SSL/TLS(Transport Layer Security) 프로토콜을 거치도록 설계
- 데이터 전송, 수신 시
    - (HTTP/TCP)가 SSL에게 데이터 전송 시 `SSL이 데이터를 (암호화/복호화)`하여 (TCP/HTTP)에게 전달
- 안전한 데이터의 전달이 브라우저와 웹 서버 사이 전달 구간에서 이뤄짐
    - 보안채널, 전송 보안이라고도 불림
- HTTPS 통신

  대칭키 방식과 공개키 방식 함께 사용

    - `대칭키 방식`
        - 실제 데이터 암호화 시 사용
    - `공개키 방식`
        - 서버에 대한 인증과 안전한 대칭키 전달 시 사용

<br/>
<br/>

### **SSL과 TLS의 차이점**

-  인터넷 상에서 데이터를 안전하게 전송하기 위한 암호화 프로토콜
  - TLS는 SSL의 후속 버전으로, SSL의 보안 문제를 개선하고 더 안전하게 만든 프로토콜
- 주요 차이점
  - `버전 차이`
    - SSL은 초기 암호화 프로토콜로, 1990년대에 넷스케이프(Netscape)에서 개발됨. SSL 3.0이 가장 마지막 버전이며, 이후 TLS로 발전함.
    - TLS는 SSL 3.0을 기반으로 개발된 새로운 프로토콜
  - `보안성 개선`
    - SSL은 Poodle 같은 취약점이 발견되면서 더 이상 안전하지 않게 되었음
    - TLS 1.2 및 TLS 1.3은 더 강력한 암호화 알고리즘을 도입하고, 보안 메커니즘을 개선하여 SSL보다 훨씬 안전함
  - `핸드셰이크 과정 차이`
    - SSL의 핸드셰이크 과정에서는 암호화된 데이터를 전송하기 전에 별도의 인증 메시지를 주고받음. TLS에서는 이 과정을 간소화하여 성능을 향상시킴
  - `지원하는 암호화 알고리즘`
    - SSL은 이전에 사용되던 일부 취약한 암호화 알고리즘을 지원했으며, 현대적인 보안 요구사항에 맞지 않음
    - TLS는 최신의 강력한 암호화 알고리즘(예: AES, ChaCha20)을 지원하며, TLS 1.3에서는 과거의 취약한 암호화 알고리즘을 완전히 제거함
  - `프로토콜 확장성`
    - TLS는 확장성이 더 뛰어나며, 지속적으로 보안 패치를 통해 새로운 기술과 요구사항을 반영할 수 있음. 
    - 예를 들어, TLS 1.3에서는 암호화와 관련된 불필요한 기능을 제거하고 성능과 보안을 동시에 강화함

|             | SSL                     | TLS                |
|-------------|-------------------------|--------------------|
| 최신 버전       | SSL 3.0 (더 이상 사용되지 않음)  | TLS 1.3 (2018년)    |
| 보안성         | 취약점 존재 (Poodle 등)       | 보안성 강화, 최신 암호화 알고리즘 |
| 핸드셰이크       | 복잡하고 느림                 | 더 간소화되고 빠름         |
| 암호화 알고리즘    | 과거의 약한 알고리즘 지원          | 최신 강력한 알고리즘 지원     |
| 사용 여부       | 사용 중단                   | 현재 표준              |

> Poodle 취약점 : https://rootable.tistory.com/407

<br/>
<br/>

## 대칭 키 암호화와 비대칭 키 암호화

HTTPS는 대칭키 암호화, 비대칭키 암호화 방식을 모두 사용하고 있습니다.

<br/>

### [ 대칭 키 암호화 ]

똑같은 개인 키를 송・수신자가 공유하여 정보를 암호화・복호화 하는 것

**대칭 키 : 어떤 정보를 암호화・복호화 할 때 사용하는 키가 동일한 경우**

- 암호화 된 정보의 전달, 확인을 위해선 송・수신자 둘 다 같은 키를 가져야 함
    - 어떤 정보가 대칭 키를 통해 암호화 되었을 시, 똑같은 키를 갖고 있는 사용자가 아닐 경우 해당 정보 확인 불가
- 키의 안전한 교환이 대칭 키 암호화 방식의 가장 중요한 부분

- 장점
    - 암・복호화 과정이 단순
    - 암・복호화 과정의 속도가 빠름
      ⇒ 송신자와 수신자가 동일한 키를 가지고 있기 때문

- 단점
    - 송・수신자 간 키 교환이 이뤄져야함
    - 키 교환 과정 중 키 노출 위험
    - 송・수신자가 늘어날수록 관리해야할 키의 증가로 관리의 어려움

<br/>

### [비대칭 키 암호화]

1개의 쌍으로 구성된 공개키/개인키로 암호화・복호화 하는 것 방식

**비대칭 키 : 어떤 정보를 암호화・복호화 할 때 사용하는 키가 서로 다른 경우**

- 암호화 방식
    - 개인 키 암호화 방식
        - 공개키는 누구나 알 수 있고, 데이터를 암호화하는 데 사용됨.
        - 개인키는 소유자만이 알고 있는 비밀 키로, 데이터를 복호화하는 데 사용됨.
        - 디지털 서명으로 활용 : 비대칭키 암호화는 디지털 서명에도 사용됨. A가 메시지에 자신의 개인키로 서명하면, B는 A의 공개키를 사용해 그 서명을 검증할 수 있음.
    - 작동 방식
      - 데이터 암호화 : A(보내는 사람)가 B(받는 사람)에게 메시지를 보낼 때, B의 공개키로 메시지를 암호화함. 
      - 복호화 : B는 자신의 개인키를 사용해 A가 보낸 암호화된 메시지를 복호화함.
    - 공개 키 암호화 방식 : 공개키를 통해 암호화하여 개인키로만 복호화 가능
        - 정보 자체에 대한 암호화가 필요 시 사용
        - 대칭 키 암호화 방식에서 키 값 교환에 따른 문제를 해결한 방법

- 장점
    - 단 하나의 공개 키를 사용함으로 모든 수신자와 개별 키를 만들 필요 없음
    - 데이터 송신과정 중 키를 탈취 당하더라도 해당 키로 복호화가 불가능하기에 공개키 방식보다 안전
- 단점
    - 암・복호화에 서로 다른 키를 사용하므로 대칭키와 비교해 느린 속도
      - 그 때문에 주로 작은 데이터나 대칭키를 안전하게 전송하는 데 사용됨.

<br/>
<br/>

## SSL Handshake 과정 (TLS Handshake)

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/fbb28c54-b26a-4d78-a9b3-51676eb3de37"  width="500" height="300"/>

<br/>

기존의 TCP/IP 3Way Handshake 과정에서 보안을 담당하는 SSL과정이 추가되어 보안이 진행된다.
> 인증서를 사용하는 이유는 서버의 신원 인증과 암호화된 통신을 위한 안전한 키 교환을 보장하기 위해서 사용함.

<br/>

### **클라이언트: (1) Client Hello 패킷전송**

클라이언트가 서버에 연결을 시도하며 **Client Hello** 패킷 전송

**Client Hello 패킷 정보**

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/d9bba651-3db8-4a47-9bc1-825b4f92ff9e" width="500" height="300">

<br/>

- 브라우저가 사용하는 SSL/TLS 버전 정보
- 브라우저가 지원하는 암호화 방식 모음(cipher suite)
    - 아래 내용을 패키지 형태로 묶어놓은 것
        - 안전한 키 교환, 전달 대상 인증, 암호화 알고리즘, 메시지 무결성 확인 알고리즘 방식
- 브라우저가 순간적으로 생성한 임의의 난수
- 이전에 SSL핸드셰이크가 완료된 상태일 경우, 그때 생성된 세션 아이디

<br/>


### **서버: (2) Server Hello 패킷 전송**

클라이언트로부터 패킷을 받아 Cipher Suite중 하나를 선택해 Server Hello에 담아 전송

**Server Hello 패킷정보**

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/bbe89dc2-3bac-4226-8576-c04ec0269014" width="500" height="300">

<br/>

- 브라우저의 암호화 방식 정보 중 서버가 지원하고 선택한 암호화 방식(이전과 달리 하나의 Cipher Suite가 포함됨을 볼수 있다.)
- 서버의 공개키가 담긴 SSL 인증서
- 서버가 순간적으로 생성한 임의의 난수
- 클라이언트 인증서 요청(선택사항)

<br/>

### **서버: (2) Certificate**

자신의 SSL인증서가 포함된 Certificate패킷 전송

**Certificate 패킷 정보**

- Server가 발행한 공개키(개인키는 서버가 소유)
- 클라이언트는 서버가 보낸 CA(인증기관)의 개인키로 암호화된 SSL인증서를 모두에게 공개된 공개키를 사용하여 복호화한다.
    - 정상적 복호화 → CA발급 증명
    - 등록된 CA 아님 or 가짜 인증서 → 브라우저에게 경고 보냄

<br/>

### **서버: (2) Server Key Exchange / ServerHello Done**

- Server Key Exchange : 서버의 공개키가 SSL인증서 내부에 없을경우, 서버가 직접 전달한다는 뜻. 공개키가 SSL인증서 내부에 있다면 해당 패킷은 생략.
- ServerHello Done : 서버가 클라이언트 헬로에 대한 응답을 완료하고, 핸드셰이크의 다음 단계를 시작하기 위한 준비가 되었음을 알리는 단계

<br/>

### **클라이언트: (3) Client Key Exchange**

- **1. 대칭키 생성**
  - 클라이언트는 핸드셰이크 과정의 이전 단계에서 받은 서버의 공개키를 사용하여 대칭키를 암호화할 준비를 함. 
  - 클라이언트는 세션 키라고도 불리는 대칭키를 생성함. 
  - 이 대칭키는 클라이언트와 서버 간의 데이터 전송을 암호화하는 데 사용됨.
- **2. 대칭 암호화**
  - 클라이언트는 생성한 대칭키를 서버의 공개키로 암호화함. 
  - 서버의 공개키는 서버 인증서에 포함되어 있으며, 클라이언트는 이 공개키를 사용하여 대칭키를 암호화함.
- **3. 암호화된 대칭키 전송**
  - 암호화된 대칭키를 Client Key Exchange 메시지의 일부로 서버에 전송함. 
  - 이 암호화된 대칭키는 서버의 개인키로 복호화될 수 있음.
- **4. 서버의 대칭키 복호화**
  - 서버는 클라이언트로부터 수신한 암호화된 대칭키를 자신의 개인키를 사용하여 복호화함.
  - 이렇게 복호화된 대칭키는 클라이언트와 서버가 실제 데이터를 암호화하고 복호화하는 데 사용됨.

<br/>

### **클라이언트: (3) ChangeCipherSpec / Finished**

- `ChangeCipherSpec패킷`은 클라이언트와 서버 모두가 서로에게 보내는 패킷으로 교환할 정보를 모두 교환한 뒤 통신할 준비가 다 되었음을 알리는 패킷
- `Finished패킷`을 보내어 SSL Handshake를 종료

<br/>

### **서버: (4) ChangeCipherSpec / Finished**

위와 동일

<br/>
<br/>

---
### Ref
https://steady-coding.tistory.com/512