# DNS에 대해 설명해 주세요.
도메인 이름 시스템 (Domain Name System)
텍스트(도메인 이름)를 IP주소로 변환해주는 것   
- URL
![URL](https://cdn.prod.website-files.com/64949e4863d96e26a1da8386/64f5f56c78d05cf501922f99_64a2ef9774661044d9755e98_URL%2520-%2520Glossary.png)
Domain Name 형식 : `...xxx.yyy.zzz`   
DNS는 .으로 구분되고, 거꾸로 읽음 `...xxx.yyy.zzz .(root)`   
![domain name](https://raventools.com/marketing-glossary/wp-content/uploads/2016/02/1354-2.jpg)   
#### domain의 종류
##### TLD(Top Level Domain) : 인터넷 주소 체계에서 가장 높은 계층의 도메인   
1. gTLD(일반 최상위 도메인)
    - `com` - 영리 목적의 기업이나 단체
    - `net` - 네트워크를 관리하는 기관
    - `org` - 비영리 기관
2. ccTLD(Country Code Top Level Domain)
   나라나 지역을 나타내는 도메인
   kr, tv 등
   - ccSLD   
    TLD로 통합해서 부르기도 함
    go.kr, ac.kr, co.kr 등
##### SLD(Second Level Domain)
사용자가 브랜드 및 웹사이트를 나타내기 위해 등록하는 도메인   
사용하기 위해 도메인 등록 기관에 도메인 이름을 등록해야 함

##### 그 이하 도메인
사용자가 임의로 지정
ex) m.yyy.zzz, test.yyy.zzz, xxx.yyy.zzz

##### 도메인 이름 -> IP 매핑 과정
![image-2](https://github.com/user-attachments/assets/7e6ce630-14ae-47fa-a457-0162a6362a8a)
- recursive query 과정 :   
    DNS resolver는 cache된 도메인 이름을 못찾았으면 recursive query를 수행하여 IP 주소를 찾습니다.
    1. root server에 요청하여 TLD server 정보를 받습니다.
    2. TLD server에 요청하여 SLD server 정보를 받습니다.
    3. SLD server 정보를 client에게 보냅니다.
    4. client는 SLD server의 IP 정보를 통해 서버랑 연결합니다.
#### DNS는 몇 계층 프로토콜인가요?
응용계층 프로토콜입니다.    
OSI 7 Layer에서는 7계층이고, TCP/IP 4 Layer에서는 4계층에 속합니다.
#### UDP와 TCP 중 어떤 것을 사용하나요?
UDP, TCP 둘 다 사용합니다.   
주로 UDP를 사용하지만, 용량이 큰 데이터를 전송하는 상황에서는 TCP를 사용합니다.   
DNS가 관리하는 전체 영역(zone)을 다른 DNS로 보내는 상황 등에서 용량이 큰 데이터를 요구합니다.
#### DNS Recursive Query, Iterative Query가 무엇인가요?
재귀적 쿼리는 DNS 리졸버가 클라이언트를 대신해 모든 필요한 DNS 쿼리를 수행하고 최종 응답을 클라이언트에게 제공합니다.(그림의 1,8) 

반복적 쿼리는 DNS 리졸버가 각 단계마다 다른 DNS 서버에 쿼리를 보내며, 최종적으로 필요한 정보를 얻을 때까지 반복적으로 쿼리를 보냅니다.(그림의 2,3,4,5,6,7)
#### DNS 쿼리 과정에서 손실이 발생한다면, 어떻게 처리하나요?
DNS 쿼리가 손실될 경우, 클라이언트와 DNS resolver는 일정 시간 후에 쿼리를 다시 시도합니다. 보통 클라이언트는 타임아웃 시간이 지나면 동일한 쿼리를 재전송합니다. 이러한 재시도는 몇 번 반복될 수 있으며, 일정 시간 후 TCP로 쿼리를 수행할 수 있습니다.
#### 캐싱된 DNS 쿼리가 잘못 될 수도 있습니다. 이 경우, 어떻게 에러를 보정할 수 있나요?
- dns 에러 확인 방법  
    dnslookup한 결과가 공식 IP와 다른 경우 캐싱된 dns에 오류가 있다는 것을 확인할 수 있습니다.    
    dns의 캐싱 위치에 따라 보정 방법이 다릅니다.
1. 클라이언트 어플리케이션의 DNS cache 초기화  
    어플리케이션에 따라 DNS를 저장하고 있을 수 있습니다.   
2. OS DNS cache 초기화   
   운영체제에서 DNS 캐시를 관리하는 주체는  dns **stub** resolver 입니다.  
   각 운영체제는  dns **stub** resolver 구현체가 다르므로 운영체제가 지원하는 명령어에 따라 dns stub resolver에 캐시 삭제를 명령할 수 있습니다.   
    ex) WINDOW : cmd prompt에서 ipconfig / flushdns 명령어 입력
    - 주의 : 운영체제에서 dns를 관리하는 주체는 dns **stub** resolver 입니다.  
    recursive dns resolver는 local dns server라 부르기도 합니다
![주의](https://www.nslookup.io/img/how-does-dns-resolver-work.3f1ba36a.jpg)
#### DNS 레코드 타입 중 A, CNAME, AAAA의 차이에 대해서 설명해주세요.
DNS 레코드는 해당 레코드와 일치하는 각 네임 서버에 저장 및 관리됩니다.   
- A : 도메인 주소와 IPv4를 매핑   
- CNAME : 도메인을 다른 도메인과 매핑, 최종적으로 A 레코드를 불러오도록 해야함   
`ex) sftp.example.com 900 IN CNAME example.com`   
- AAAA : 도메인 주소와 IPv6를 매핑  

- 도메인 주소와 IP 주소를 매핑 vs 도메인을 다른 도메인과 매핑
  도메인과 IP 주소를 매핑하면 바로 주소로 찾아가므로 빠름
  도메인을 다른 도메인과 매핑하면 관계를 정의할 수 있음(변경에 유연)  

#### hosts 파일은 어떤 역할을 하나요? DNS와 비교하였을 때 어떤 것이 우선순위가 더 높나요?
- hosts 파일 
  운영체제 내에서 도메인 이름과 IP주소를 직접 매핑하는 기능을 지원

  DNS보다 우선순위가 더 높습니다. 따라서 해킹에서는 공격자가 hosts 파일을 조작하여 DNS 스푸핑을 시도합니다.
