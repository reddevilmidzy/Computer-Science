# 로드 밸런싱

로드 밸런싱이란 서버에 부하가 몰릴때 작업(Load)을 나눠서(Balancing)처리하는 작업을 말한다. 즉 트래픽을 원할하게 하기 위한 작업이다. 
부하 분산 장치라고도 불린다.  
서버에 많은 트래픽이 몰릴 경우 해결방법은 서버를 들리는 방법인 **Scale-out**과 장비를 업그레이드하는 **Scale-up**이 있다. Scale-out 같은 경우에 로드밸런싱을 사용한다.
만약 로드밸런싱 없이 서버만 늘렸다면 A 서버에는 999명이 몰리는데 새로 만든 B 서버에는 1명만 접속하는 문제가 발생하게 된다.
서버가 수용할 수 있는 작업이 한정되어 있기 때문에 로드 밸런싱을 사용하여 뒤쪽에 있는 여러 서버들로 나눔으로써 시스템 전체적으로
처리 가능한 트래픽양을 확장하는 것이다. 또한 **헬스체크**(HC, Health Check)를 사용하여 서버 감시 후 장애가 발생한 서버는 부하 분산 대상에서
제외해 서비스의 가용성을 향상시킨다.  


## 로드 밸런싱 기능
### NAP(Network Address Translation)  
* 사설 IP 주소를 공인 IP 주소로 혹은 공인 IP 주소를 사설 IP로 변환해주는 기능
* SNAT(Source Network Address Translation) 내부에서 외부로 트래픽이 나가는 경우, 사설 IP 주소 -> 공인 IP 주소 
* DNAT(Destination Network Address Translation) 외부에서 내부로 트래픽이 들어오는 경우, 공인 IP 주소 -> 사설 IP 주소

### Tunneling
* 데이터를 캡슐화해서 연결된 상호 간에만 캡슐화된 패킷을 구별해 캡슐화를 해제 가능
* 로드 밸런서의 **VIP** 주소로 가는 요구 패킷이 로드 밸런싱 서버에 전달되면 패킷의 목적지 주소와 포트를 검사후 가상 서버 정책과 일치 할 경우 스케줄링에 따라 실제 작업 서버로 전달될 때 캡슐 형식 사용
### DSR(Direct Server Routing)
* 서버에서 클라이언트로 되돌아가는 경우 목적지를 클라이언트로 설정한 다음, 네트워크 장비나 로드 밸런서를 거치지 않고 바로 클라이어트로 찾아가는 방식
* 로드 밸런서의 부하를 줄여줌

<br>

### **VIP**(Virtual IP)
> VIP란 로드 밸런싱의 대상이 되는 여러 서버를 대표하는 가상의 IP이다. 클라이언트들은 서버의 IP로 직접 요청을 하는 것이 아니라 LB가 가지고 있는 VIP를 대상으로 요청한다.
> 그리고 LB는 설정된 부하 분산 방법에 따라 각 서버로 요청을 분산한다.


## NLB과 ALB

로드 밸런싱을 사용하는 계층은 전송 계층과 애플리케이션 계층이다. 전송 계층에서 사용하는 로드 밸런싱은 **NLB**(Network LoadBalancer) 혹은 L4, 
애플리케이션 계층에서 사용하는 로드 밸런싱은 **ALB**(Application LoadBalancer) 혹은 L7이라고 부른다. 

| -       | NLB                                                                                                                         | ALB                                                                                                                           |
|---------|-----------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 네트워크 계층 | Layer 4                                                                                                                     | Layer7                                                                                                                        |
| 특징      | TCP/UDP 포트 정보를 바탕으로함                                                                                                        | TCP/UDP 정보는 물론 HTTP의 URL, FTP의 파일명, 쿠키 정보등을 바탕으로 함                                                                            |
| 장점      | 1. 데이터 안을 들여다보지 않고 패킷 레벨에서만 로드를 분산하기 때문에 속도가 빠르고 효율이 높음 <br><br> 2. 데이터의 내용을 복호화할 필요가 없기에 안전함 <br><br> 3. L7로드밸런서보다 가격이 저렴함 | 1. 상위 계층에서 로드를 분산하기 때문에 훨씬 더 섬세한 라우팅이 가능함 <br><br> 2. 캐싱 기능을 제공함 <br><br> 3. 비정상적인 트래픽을 사전에 플터링할 수 있어 서비스 안정성이 높음             |
| 단점      | 1. 패킷의 내용을 살펴볼 수 없기 때문에 섬세한 라우팅이 불가능함 <br><br> 2. 사용자의 IP가 수시로 바뀌는 경우라면 연속적인 서비스를 제공하기 어려움                                  | 1. 패킷의 내용을 복호화해야 하기에 더 높은 비용을 지불해야 함 <br><br> 2. 클라이언트가 로드밸런서와 인증서를 공유해야하기 때문에 공격자가 로드밸런서를 통해서 클라이언트에 데이터에 접근할 보안 상의 위험성이 존재함 |


## 로드 밸런싱 알고리즘

### 라운드로빈 방식(Round Robin Method)
* 요청이 들어오는 순서대로 서버마다 균등하게 요청을 배부하는 방법
* 같은 스펙을 갖고 있는 서버에서 연결(세션)이 오래 지속되지 않는 경우 적합

### 가중 라운드로빈 방식(Weighted Round Robin Method)
* 서버마다 가중치를 매기고 가중치가 큰 서버에 우선적으로 요청을 배분하는 방법
* 서버의 트래픽 처리 능력이 상이한 경우 적합

### IP 해시 방식(IP Hash Method) 
* 클라이언트의 IP 주소를 특정 서버로 매핑하여 요청을 처리하는 방식
* 사용자의 IP를 해싱하여 로드를 분배하기에 사용자는 항상 동일한 서버로 연결되는 것을 보장

### 최소 연결 방식(Least Connection Method)
* 요청이 들어온 시점에 가장 적은 연결상태를 가진 서버에 우선적으로 트래픽을 배분하는 방법
* 세션이 길어지는 일이 잦거나, 서버에 분배된 트래픽들이 일정하지 않은 경우에 적합

### 최소 리스폰타임(Least Response Time Method)
* 서버의 현재 연결 상태와 응답시간을 모두 고려하여 트래픽을 배분하는 방법
* 가장 적은 연결 상태와 가장 짧은 응답시간을 보이는 서버에 우선적으로 로드를 배분


---

### reference

[로드 밸런싱 종류와 알고리즘](https://dev.classmethod.jp/articles/load-balancing-types-and-algorithm/)  
[로드밸런서의 개념과 특징](https://m.post.naver.com/viewer/postView.nhn?volumeNo=27046347&memberNo=2521903)  
[로드밸런서의 종류와 동작방식](https://deveric.tistory.com/91)  
[로드 밸런서란?](https://nesoy.github.io/articles/2018-06/Load-Balancer)  
[[네트워크]로드 밸런서](https://steady-codng.tistory.com/535)