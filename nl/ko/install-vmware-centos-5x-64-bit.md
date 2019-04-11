---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# CentOS 5.x에 VMware 설치(64비트)
{: #installing-vmware-on-centos-5-x-64-bit-}

시작하기 전에 루트 사용자로 서버에 로그인하십시오.

## VMware 설치 준비 
{: #preparing-to-install-vmware}

1. [VMware 설치 프로그램 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://vmware.com/download/server/){: new_window}을 다운로드하십시오. 다운로드 링크를 클릭하고 EULA를 승인하고 LinuxTarball을 다운로드하십시오(이 예에서는 VMware-server-1.0.3-44356.tar.gz).

* `# wget –O vmware-server.tar.gz http://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. 소프트웨어를 다운로드한 후에 라이센스 키를 얻어야 합니다(VMWare Server의 무료 버전에서는 무료). 등록하려면 [VMware ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://register.vmware.com/content/registration.html){: new_window}을 참조하십시오.

3. .tar 파일을 추출하십시오. 

* `# tar -xzvf vmware-server.tar.gz`

## 설치를 시작하기 전에 
{: #before-you-begin-installation}

설치를 시작하기 전에 다음 전제조건을 검토하십시오. 

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## VMWare 서버 설치
{: #installing-vmware-server}

1. 다음 디렉토리로 이동하십시오. 

* `# cd vmware-server-distrib/`

2. VMware 설치 스크립트를 실행하십시오. 

* `# ./vmware-install.pl`

3. 설치 시 기본 질문이 표시되며, VMware의 특정 파트를 작성하고 설치할 디렉토리를 알 수 있습니다. 여기서는 기본값을 승인하십시오. 설치를 계속하려면 라이센스 계약에 동의하십시오.

4. 다음 질문이 표시될 수 있습니다. "VMware Server의 사전 빌드된 vmmon 모듈이 실행 중인 커널에 적합하지 않습니다. 이 프로그램을 사용하여 시스템에 대한 vmmon 모듈을 빌드하시겠습니까(시스템에 C 컴파일러가 설치되어 있어야 함)?"
* 이 질문에 **예**로 답하십시오(기본값임). 

## VMware 네트워킹 설정
{: #setting-up-vmware-networking}

다음 질문으로 프롬프트가 표시됩니다. 

1. 가상 머신을 네트워킹하시겠습니까?
* **예**로 답변하십시오. 가상 머신에서 인터넷에 액세스할 수 있도록 공용 네트워크 디바이스에 대한 네트워크 설정을 작성해야 합니다. 

2. "컴퓨터에 eth0, eth1 등 사용 가능한 이더넷 인터페이스가 여러 개 있습니다. 어떤 인터페이스로 vmnet0에 브릿징하시겠습니까?"
* 모든 {{site.data.keyword.BluSoftlayer}} 서버가 설정 및 실행되는 방법은 eth1에서 공용 네트워크가 실행되고 eth0에서 사설 네트워크가 실행된다는 점을 기억해 두십시오. VMware에서 vmnet0에 대한 기본 브릿지 디바이스가 eth0입니다. Enter를 누르는 대신 eth1을 입력하십시오.

## 사설 네트워크 브릿징
{: #bridging-the-private-network}

**예** 또는 **아니오**로 대답할 수 있는 다음 질문에 대한 프롬프트가 표시됩니다.
*"다른 브릿지된 네트워크를 구성하시겠습니까?"

사설 네트워크에서 서비스 또는 기타 애플리케이션을 실행하려는 경우, 사용자가 사설 네트워크를 사용하지 않는 것을 아는 경우가 아니면 "예" 로 진행하십시오. 네트워크 브릿지가 사설 네트워크로 작성됩니다. Enter를 누르고 나면 서버에 두 개의 네트워크 카드만 있기 때문에 자동으로 eth0을 인터페이스로 사용합니다.

## 다른 네트워킹 설정
{: #other-networking-settings}

VMware 서버의 네트워크 설정에 대한 몇 가지 다른 질문이 있습니다. 다음과 같은 권장사항으로 진행하십시오.

* *"가상 머신에서 NAT 네트워킹을 사용할 수 있습니까?"*

- "예"로 진행합니다.

* *"이 프로그램이 사용되지 않는 사설 서브넷을 프로브하도록 하시겠습니까?"*

- "예"로 진행합니다.

- 이 프로세스가 완료된 후 다른 NAT 네트워크를 구성하지 않는지 확인하십시오.

* *"가상 머신에서 호스트 전용 네트워킹을 사용할 수 있습니까?"*

- "예"로 진행합니다.

* *"이 프로그램이 사용되지 않는 사설 서브넷을 프로브하도록 하시겠습니까?"*

- "예"로 진행합니다.

- 이 프로세스가 완료된 후 다른 호스트 전용 네트워크를 구성하지 않는지 확인하십시오.

## 청취 포트 지정
{: #specifying-listening-port}

요청하는 다음 질문은 VMware Server가 청취하도록 할 포트입니다. 기본 포트를 904로 남겨둘 수 있습니다.

## 가상 머신 저장
{: #storing-the-virtual-machines}

설치 프로그램이 요청하는 다음 질문은 *"어느 디렉토리에서 가상 머신 파일을 유지하시겠습니까?*입니다. 기본 위치는 /var/lib/vmware/virtual 머신입니다. 중복 RAID 배열 또는 대규모 보조 하드 디스크 등과 같이 많은 공간이 있는 위치에 가상 머신을 배치하도록 하십시오. 항상 가상 머신을 위한 충분한 공간이 있는지 확인하십시오. 이 경우 대규모 디스크에 마운트된 마운트 지점 /data/vm을 사용할 수 있습니다. 

## VMware용 일련 번호 제공
{: #providing-the-serial-number-for-vmware}

설치의 마지막 부분에는 VMware 라이센스 키 및 일련 번호를 삽입해야 합니다. 라이센스 키가 아직 없는 경우 [VMware 사이트 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://register.vmware.com/content/registration.html){: new_window}을 참조하십시오. 이 VMware Server의 일련 번호가 있는 경우에는 프롬프트에 이를 삽입한 후 Enter를 누르십시오.

이제 다음과 유사한 내용이 표시됩니다.

    “The configuration of VMware Server 1.0.3 build-44356 for Linux for this running kernel completed successfully.”

VMware는 이제 서버에서 설정됩니다. 이제 VMware Server용 GUI 클라이언트인 VMware Server Console을 다운로드하여 가상 머신을 구성 및 설치해야 합니다.

## VMware 서버 콘솔 다운로드
{: #downloading-vmware-server-console}

VMware Server Console은 VMware Server용 클라이언트 애플리케이션입니다. 콘솔을 사용하여 가상 머신을 작성, 구성 및 설치할 수 있는 VMware Server를 관리할 수 있습니다. 이 애플리케이션을 설치하려면 [VMware 다운로드 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://vmware.com/download/server/){: new_window}에서 VMware Server Windows VMware Server Windows 클라이언트 패키지를 다운로드하십시오. 이 패키지는 .zip 파일입니다. 다운로드하고 나면 패키지를 추출하여 VMware-console-1.0.3-x 파일을 설치하십시오. 설치가 완료되면 VMware Server Console의 설치가 완료되며 VMware Server 구성 준비가 됩니다.

## VMware 콘솔에 로그인
{: #logging-in-to-the-vmware-console}

VMware Server Console을 설치한 컴퓨터에서 이를 여십시오. 로드가 완료되면 "스위치 호스트"(로그인) 화면이 프롬프트됩니다. VMware Server는 사용자를 인증하기 위해 Linux 시스템 사용자 이름 및 비밀번호를 사용하므로 VMware에 로그인하려면 사용자 이름(특히 root)을 사용해야 합니다. 다음 인증 정보를 사용하십시오. 

* **호스트 이름:** IP 주소 및 포트(예: 67.228.160.201:904)<br />
* **사용자 이름:** root<br />
* **비밀번호:** 비밀번호(시스템의 실제 root 비밀번호 사용)

## 방화벽 규칙 구성(IPTables)
{: #configuring-the-firewall-rules-iptables-}

VMware 서버 연결에 문제가 있고 인증 문제가 아니라면(사용자 이름 및 비밀번호 오류가 발생하는 경우 잘못된 사용자 또는 비밀번호를 가지게 됨), 방화벽으로 인해 VMware Server에 대한 연결을 차단당할 수 있습니다. 해결책으로 다음 IPTable 규칙을 /etc/sysconfig/iptables 파일에 추가하십시오. **참고:** 이름 지정 규칙이 서버 구성을 따르는지 확인하십시오.

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
