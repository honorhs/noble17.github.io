---
title:  "centOS를 이용하여 DHCP server 흉내내기"
date:   2016-03-28
categories: [학습]
comments: true
tags: [dhcp, network]
---

## 환경

- centOS 6.7

## 설치

-  CentOS 6.7 Live 버전

## dhcp 서버 설치

`yum -y install dhcp`

- yum의 -y은 묻지않고 그냥 바로 설치하는 옵션 
- 권한 문제로 설치가 되지 않을수도 있다. 그럴 때 root권한으로 설치

## dhcpd.conf수정하기

`vi /etc/dhcp/dhcpd.conf`

---
	# 기본설정으로 subnet마다 할당될 때 특정한 할당옵션이 없다면 위의 기본옵션을 따르게 된다.
	option domain-name "example.org";   # 네임서버를 이름으로 찾을 때 사용된다.
	option domain-name-servers 168.126.63.1, 168.126.63.2, 8.8.8.8;   # 네임서버
	option subnet-mask 255.255.255.0;   # 서브넷 마스크
	option routers 20.20.20.1;   # Gateway 주소
	option broadcast-address 20.20.20.255;   # BroadCast 주소
	default-lease-time 600;   # 할당된 ip주소를 가질 수 있는 시간
	max-lease-time 7200;   # 할당받은 ip주소를 가지고 있을 수 있는 최대 시간
    
    # Client가 요청이 왔을 때 할당해주는 네트워크 주소
    subnet 20.20.20.0 netmask 255.255.255.0 {
    	range 20.20.20.2 20.20.20.254
        
        # mac주소를 이용해서 고정ip 설정
        host client-name {
        	hardware ethernet xx:xx:xx:xx:xx:xx;   # 특정 mac주소가 dhcp주소를 요청하면
            fixed-address 20.20.20.x;   # 특정 ip를 제공해준다.
    	}
    }
    
## dhcp 요청을 받을 인터페이스를 설정

`vi /etc/sysconfig/dhcpd`

---

	DHCPDARGS=eth0
    
    
    
## DHCP 서버 실행

`service dhcpd.start`   # DHCP 서버 실행 <br>
`service ntpd start`   # ntp 서버 실행 <br>
`chkconfig dhcpd on`   # DHCP server가 리눅스 부팅시 실행되도록 설정 <br>
`chkconfig ntpd on`   # ntp 서버가 리눅스 부팅시 실행되도록 설정 <br>
