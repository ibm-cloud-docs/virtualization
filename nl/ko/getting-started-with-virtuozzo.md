---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Virtuozzo 시작하기
{: #getting-started-with-virtuozzo}

## PIM(Parallels Infrastructure Manager)에 액세스
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. [{{site.data.keyword.slportal_full}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/){: new_window}에서 Bare Metal Server에 대한 정보를 얻으십시오.
    1. **디바이스** 메뉴에서 **디바이스 목록**을 선택하십시오.
    2. 서버를 클릭하십시오.
    3. 공인 IP 주소 및 비밀번호를 찾으십시오. 
5. HTTPS를 사용하여 선호하는 브라우저에서 서버의 공인 IP 주소로 이동하십시오. PIM을 통해 Virtuozzo를 관리하려면 Firefox, Opera 또는 Internet Explorer를 사용하십시오.

JavaScript가 필요합니다. JavaScript가 없으면 존재하지 않는 페이지로 경로 재지정될 수 있으며 PIM이 작동하지 않게 됩니다.
{:tip}

6. 사용자 이름 및 비밀번호를 입력하여 PIM에 로그인하십시오.

{{site.data.keyword.slportal}}에 나열된 대로 사용자 이름은 "root"이며 비밀번호는 서버에 대한 루트 비밀번호입니다.
{:tip}

## 템플리트 설치 및 캐싱
{: #installing-and-caching-templates}

1. PIM 화면에서 **관리** > **업데이트**를 선택하십시오. 
2. 업데이트할 서버로 이동하십시오.
3. **템플리트 업데이트**를 클릭하십시오.
4. 설치 또는 업데이트할 추가 템플리트를 선택하십시오.
5. 업데이트하려는 서버를 선택하려면 **템플리트** > **OS 템플리트**를 클릭하십시오. 
6. 서버 및 캐시에서 사용할 OS 템플리트를 선택하십시오. 일반적으로 모든 템플리트를 시작할 필요는 없습니다. 사용하려는 캐시 템플리트만 캐시하십시오. 이러한 템플리트는 `/vz` 파티션에서 상당한 크기의 영역을 차지합니다. 
7. 노드가 하나만 있는 경우, **다음 캐시**를 클릭하십시오.
8. 전체 템플리트 RPM가 다운로드 및 설치될 때까지 기다리십시오. 캐싱 상태를 보려면 **세부사항**을 클릭하십시오. 

## 컨테이너에 대한 네트워크 범위 설정
{: #setting-up-network-range-for-containers}

서버에서 템플리트를 다운로드 및 캐시하는 동안 사용자는 컨테이너에 대한 네트워크 범위를 설정할 수 있습니다. 

1. **선택** > **네트워크**를 클릭하십시오.
2. **새 IP 범위**를 선택하십시오.
3. 컨테이너에 대한 추가 IP 주소를 추가하십시오. 가상 서버에 대한 추가 IP 주소를 구입하지 않은 경우, [{{site.data.keyword.slportal}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/){: new_window}으로 이동하십시오. **네트워크** > **IP 관리** > **VLAN** > **주문 IP**를 선택하십시오.
4. 하드웨어 노드로 이동한 후 **컨테이너** > **새 컨테이너**를 클릭하십시오. 
5. 필요에 맞게 정보를 완성하십시오. 이 예에서는 slm.512MB를 기본값으로 사용하십시오.
6. 완전한 도메인 이름으로 호스트 이름을 입력하고 DNS 서버를 추가하고 필요한 경우 검색 도메인을 추가하십시오. 
7. 리소스 매개변수를 검토하십시오. 

너무 많은 매개변수를 추가하여 데이터, 디스크 공간 또는 메모리를 처리할 수 있는 컨테이너의 기능을 저해하지 않도록 주의하십시오. 리소스의 서버를 제공하지 않을 수 있습니다. 서버를 제공하지 않으면 속도가 느려지거나 예기치 않은 중지 프로세스가 발생할 수 있습니다. 이러한 설정을 이해하지 못한다면 서버 관리자 또는 [서드파티 관리 회사 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://developer.ibm.com/bluemix/){: new_window}에 문의하십시오.
{:tip}

8. **다음**을 클릭하기 전에 **유효성 검증**을 클릭하십시오. 유효성 검증은 설정이 서로 충돌하지 않도록 해줍니다.
9. 설치하려는 애플리케이션을 선택하고 **다음**을 클릭하십시오.
10. 구성을 검토한 후 **작성**을 클릭하십시오. 그러고 나면 컨테이너가 작성됩니다. 
11. 이제 **컨테이너**로 돌아가 새 컨테이너를 보고 자동으로 지정된 IP 주소를 얻을 수 있습니다(이전에 추가한 IP 주소에서).
12. 이제 서버에 로그인하고 이를 사용할 수 있습니다. 

        myname/myserver[0]~$ ssh 1.234.123.4
        Warning: Permanently added '1.234.123.4' (DSA) to the list of known hosts.
        root@1.234.123.4's password:
        [root@test01 ~]# ip a s
        1: lo: mtu 16436 qdisc noqueue
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
            inet6 ::1/128 scope host
              valid_lft forever preferred_lft forever
        3: venet0: mtu 1500 qdisc noqueue
            link/void
            inet 127.0.0.1/32 scope host venet0
            inet 1.234.123.4/32 brd 1.234.123.4 scope global venet0:0
        [root@test01 ~]# ps auxww
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         1  0.0  0.1  10328   744 ?        Ss   13:23   0:00 init [3]
        root     20419  0.0  0.1  12580   632 ?        Sroot     21726  0.0  0.2  60532  1220 ?        Ss   13:23   0:00 /usr/sbin/sshd
        root     21737  0.0  0.1  22512   892 ?        Ss   13:23   0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
        root     21756  0.0  0.3  62732  2052 ?        Ss   13:23   0:00 sendmail: accepting connections
        smmsp    21764  0.0  0.3  57608  1752 ?        Ss   13:23   0:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue
        root     21776  0.0  2.0 283796 10728 ?        Ss   13:23   0:00 /usr/sbin/httpd
        root     21786  0.0  0.2  20832  1144 ?        Ss   13:23   0:00 crond
        root     21796  0.0  0.1  46648   800 ?        Ss   13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        apache   21797  0.0  1.1 283932  5788 ?        S    13:23   0:00 /usr/sbin/httpd
        root     21798  0.0  0.1  46648   544 ?        S    13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        root     23573  0.0  0.6  85876  3240 ?        Rs   13:45   0:00 sshd: root@pts/0
        root     23594  0.0  0.3  12044  1604 pts/0    Ss   13:45   0:00 -bash
        root     23626  0.0  0.1  10700   952 pts/0    R+   13:45   0:00 ps auxww

        [root@test01 ~]# df -h
        Filesystem            Size  Used Avail Use% Mounted on
        vzfs                  1.0 G   64 M  961 M   7% /
        none                 1004 M  4.0 K 1004 M   1% /dev
        [root@test01 ~]#
