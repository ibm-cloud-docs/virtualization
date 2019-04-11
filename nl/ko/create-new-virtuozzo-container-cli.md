---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 새 Virtuozzo 컨테이너(CLI) 작성
{: #creating-a-new-virtuozzo-container-cli-}

## 새 Virtuozzo 컨테이너(CLI) 작성 방법
{: #how-to-create-a-new-virtuozzo-container-cli-}

다음 단계를 완료하여 Virtuozzo 서버에서 새 컨테이너를 작성하십시오.

1. 호스트에서 컨테이너의 목록을 확인하십시오. 

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. 열린 ID#로 컨테이너를 작성하십시오. 숫자는 100보다 커야 하며 아직 사용 중이 아니어야 합니다. 이 예에서는 122를 사용합니다.

       [root@vztrain01 ~]# vzctl create 122 --pkgset redhat-as3-minimal-x86_64  --config basic

        Creating Container private area (redhat-as3-minimal-x86_64/20080630)

        Container is mounted

        Postcreate action done

        Container is unmounted

        Container private area created

        Container registered succesfully

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

           122          - stopped   -               -

컨테이너를 작성한 후에는 VZ가 중지 상태가 됩니다. 어떤 패키지가 설치되었는지 확실하지 않으면 `vzpkgls` 명령을 사용할 수 있습니다. 이 명령은 서버에 설치되어 있는 Virtuozzo OS 및 애플리케이션 패키지의 리스트를 보여줍니다. 예를 들어 RedHat 템플리트가 사용되지만 Virtuozzo는 RedHat, CentOS, Debian, Fedora Core, SUSE 등의 여러 운영 체제를 지원합니다.

3. 컨테이너에 대한 기본 구성을 검토하고 필요한 변경을 수행하십시오. 

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. 컨테이너를 시작하십시오.

        [root@vztrain01 ~]# vzctl start 122

        Starting Container ...

        Container is mounted

        Setup slm memory limit

        Setup slm subgroup (default)

        Setting devperms 20002 dev 0x7d00

        Adding port redirection to Container(1): 4643 8443

        Adding IP address(es):

        Configure meminfo: 65536

        Container start in progress...

잠시 후에 컨테이너가 실행됩니다. `vzctl` 상태 122 명령으로 다시 상태를 확인할 수 있습니다. 

5. 컨테이너를 관리하는 데 필요한 매개변수를 추가하십시오.

        [root@vztrain01 ~]# vzctl set 122 --ipadd 12.34.234.142 --hostname vz122.domain.com --userpasswd root:password123 --save

        Hostname for Container set: vz122.domain.com

        Adding IP address(es) to pool: 12.34.234.142

        Adding IP address(es): 12.34.234.142

        Shutting down loopback interface:  [  OK  ]

        Setting network parameters:  [  OK  ]

        Bringing up loopback interface:  [  OK  ]

        Bringing up interface venet0:  [  OK  ]

        Changing password for user root.

        passwd: all authentication tokens updated successfully.

        Saved parameters for Container 122

명령행을 사용한 새 Virtuozzo 컨테이너의 기본 설정이 완료되었습니다.
