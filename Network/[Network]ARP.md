# ARP

**ARP**(Address Resolution Protocol)은 물리 주소인 **MAC**주소를 논리 주소인 **IP**주소로 변경시켜주는 프로토콜이다. 
ARP에서 수신지 IPv4 주소로부터 수신지 MAC 주소를 구하는데, 이것을 **주소 결정**이라고 부른다. IPv6 주소의 주소 결정은 ARP가 아닌
ICMPv6에서 수행한다.

## ARP의 프레임 포멧

* 하드웨어 타입
* 프로토콜 타입
* 하드웨어 주소 크기
* 프로토콜 주소 크기
* 오퍼레이션 코드
* 송신지 MAC 주소/송신지 IPv4 주소
* 목표 MAC 주소/목표 IPv4 주소


## ARP에 의한 주소 결정 흐름

* ARP Request : 브로드캐스트 통신
* ARP Reply : 유니캐스트

