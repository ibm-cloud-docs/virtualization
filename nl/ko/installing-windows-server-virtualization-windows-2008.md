---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Windows 2008에 Windows Server 가상화 설치
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## 시작하기 전에
{: #before-you-begin-win-2008}

시작하기 전에 다음 전제조건을 검토하십시오. 

### 하드웨어
{: #hardware}

* BIOS에서 사용 가능한 하드웨어 지원 가상화 기술
* Intel VT
* AMD-V
* BIOS에서 사용 가능한 데이터 실행 방지
* Intel XD(Execute Disable)
* AMD NX(No Execute)

### 소프트웨어
{: #software}

Windows Server 가상화를 위한 **역할** 옵션을 사용으로 설정하려면 몇 개의 패치를 설치해야 합니다.
1. 브라우저 창을 열고 %sysdir%\Windows\wsv를 찾아보십시오(일반적으로 C:\Windows\wsv임). 두 개의 파일이 해당 폴더에 있습니다.
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. 이러한 패치는 임의의 순서로 설치할 수 있습니다. 두 패치를 모두 설치한 후 시스템을 다시 시작하십시오.

## 설치
{: #installation}

1. 시스템이 다시 시작되면 **역할 **을 시스템에 추가해야 합니다.
2. 역할을 추가하고 나면 **가상 네트워크 작성** 대화 상자가 표시됩니다.
**참고:** 네트워크 연결이 잠시 끊깁니다.
3. 사설 네트워크 어댑터인 **로컬 영역 연결**을 선택하십시오. **계속**을 클릭하십시오. 설치가 계속되며, 다시 시작해야 합니다.
4. 시스템 다시 시작 후에 공용 연결을 통해 시스템에 로그인하십시오. **참고:** 이 연결을 설치하는 데 사용한 것과 동일한 로그인 인증 정보로 로그인해야 합니다. 인터페이스에 대한 네트워크 연결을 잃게 됩니다. 그렇지 않은 경우 다음 오류가 발생할 수 있습니다.
    * *Attempt to configure Windows Server Virtualization failed with error code 0x80078000.*
이 오류를 해결하려면 Windows 가상화를 위해 관리 콘솔에서 **시작 > 프로그램 > 관리 도구 > Windows 가상화 관리**로 이동하십시오. 
5. 오른쪽 분할창에서 적절한 서버를 클릭하십시오. 
6. 그런 다음 조치 분할창에서 **가상 네트워크 관리자**를 클릭하십시오. 이제 **가상 네트워크 스위치 관리**가 표시됩니다.
7. 왼쪽 분할창에서 **새로 추가** 네트워크 스위치 아래에서 네트워크 스위치를 클릭하십시오.
8. 네트워크 스위치의 이름을 개인용으로 바꾸십시오.
9. **연결**의 실제 네트워크 어댑터를 선택하고 첫 번째 네트워크 어댑터를 선택하십시오. 이제 모든 네트워크 프로토콜이 사설 네트워크 인터페이스에서 바인드 해제됩니다.
10. **중요** 네트워크 연결을 개인용으로 다시 설정하려면 사설 인터페이스가 **아닌** 새 스위치 디바이스를 구성해야 합니다. **시작 > 설정 > 네트워크 연결**로 이동하십시오. **로컬 영역 연결 2**라고 하는 새 디바이스가 표시됩니다.
11. 이 연결을 마우스 오른쪽 단추로 클릭하고 **특성**으로 이동하십시오.
12. **ipv4** 및 해당 특성을 선택하십시오. 사설 네트워크 인터페이스 IP 주소, 넷마스크, DNS 서버를 사용하여 이 디바이스를 구성해야 합니다. 
13. **확인 > 닫기**를 클릭하십시오. 이 구성은 개인용으로 네트워킹을 다시 사용 가능하게 합니다. 사설 IP에 대해 ping을 실행하여 네트워크가 사설인지 확인할 수 있습니다. 

공용 네트워크를 설정하려면 사설 IP에 대해 RDP를 실행하십시오. 

### 공용 스위치 추가
{: #adding-a-public-switch}

공용 스위치 추가는 사설 스위치 추가와 동일한 프로세스로 진행됩니다.
1. **가상 네트워크 스위치 관리**로 다시 돌아가 **새 네트워크 스위치 추가**를 선택하십시오.
2. 네트워크 스위치 유형으로 **외부**를 선택하고 **추가**를 클릭하십시오.
3. 스위치 이름을 **공용**으로 바꾸고 **실제 네트워크 어댑터**를 선택하십시오.
4. 두 번째 네트워크 어댑터를 선택하고 **확인**을 클릭하십시오. 이 구성으로 인해 공용 포트는 네트워크에 응답하지 않습니다. 적절한 설정을 사용하여 개인용으로 수행한 것처럼 새 공용 스위치 인터페이스를 구성하십시오.
