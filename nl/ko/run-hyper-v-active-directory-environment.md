---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# 활성 디렉토리 환경에서 Hyper-V 실행
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## 시작하기 전에
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

활성 디렉토리 환경에서 Hyper-V 실행을 시작하기 전에 다음 사항이 필요합니다. 

* Hyper-V(OS의 전체 또는 코어 설치)를 실행하는 64비트 Windows 2008 Datacenter 서버.
* Hyper-V를 관리하는 데 사용되는 도메인의 글로벌 그룹.
* 작업 중인 컴퓨터에서 Hyper-V 서버에 대한 변경사항을 사용하도록 하기 위한 도메인 관리 액세스.

## Hyper-V 서버 구성
{: #configuring-the-hyper-v-server}

1. Hyper-V 서버에 대한 관리 연결을 여십시오.

2. **Hyper-V** 그룹을 **분산 COM 사용자** 그룹에 추가하십시오.

3. **Hyper-V** 그룹을 **CIMV2 및 가상화** 네임스페이스에 추가하십시오.

4. **Hyper-V** 그룹을 Hyper-V 서버의 Hyper-V용 권한 부여 저장소에 추가하십시오.

5. **Hyper-V** 그룹 권한을 Hyper-V 서버의 Hyper-V 디렉토리에 제공하십시오.

## 원격 관리 연결 설정
{: #setting-up-remote-management-connection}

도메인 관리 권한으로 도메인의 컴퓨터에 로그인하도록 하십시오.

1. **제어판 > 관리 도구 > 컴퓨터 관리**로 이동하십시오.

2. 조치 메뉴에서 **다른 컴퓨터에 연결**을 선택하십시오.

3. 서버 이름 또는 IP를 입력하고 **확인**을 클릭하십시오.

## 분산 COM 사용자 그룹을 Hyper-V 서버에 추가
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

먼저 Hyper-V 서버의 분산 COM 사용자 그룹에 Hyper-V 그룹을 추가해야 합니다.

1. **시스템 도구 > 로컬 사용자 및 그룹 > 그룹 > 분산 COM 사용자 > 그룹에 추가**로 이동하십시오.

2. **추가**를 클릭하고 Hyper-V 그룹의 그룹 이름을 입력하고 **확인**을 클릭하십시오.

## CIMV2 및 가상화 네임스페이스용 서버에 대한 원격 액세스 허용
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

가상화 및 CIMV2용 서버에 대한 원격 액세스를 위해 권한을 업데이트할 수 있습니다.

1. 컴퓨터 관리 창에서 **서비스 및 애플리케이션 > WMI 제어**를 선택하십시오.

2. 마우스 오른쪽 단추를 클릭하고 **특성 > 보안 > 루트 > CIMV2 > 보안**으로 이동하십시오.

3. Hyper-V 그룹을 추가한 후 이를 선택하고 **고급**을 클릭하십시오.

4. 새 그룹이 선택되어 있는지 확인하고 **편집**을 클릭하십시오.

5. **적용 대상:**을 **네임스페이스 및 모든 하위 네임스페이스**로 변경하십시오.

6. **계정 및 원격 사용**에 대해 **허용**이 선택되었는지 확인하십시오. 

7. **이 컨테이너 내의 오브젝트 및/또는 컨테이너에 대해서만 이러한 권한 적용**을 선택하고 **확인**을 클릭하십시오.

8. 가상화를 위해 이러한 단계를 반복하십시오. 

## 권한 부여 저장소 업데이트
{: #updating-authorization-store}

Hyper-V에 대한 권한 부여 저장소는 Hyper-V에 도메인 그룹을 제공하는 최종 컴포넌트입니다.

1. 실행 메뉴 또는 명령 프롬프트에서 `azman.msc` 명령을 실행하여 권한 관리자를 여십시오. 

2. 조치 메뉴에서 **권한 부여 저장소 열기**를 선택하십시오.

3. **XML**이 선택되어 있는지 확인하십시오. 이제 Hyper-V 서버에서 `InitalStore.xml`에 원격으로 액세스해야 합니다. 다음 경로를 사용하십시오. 

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. **Hyper-V 서비스 > 역할 지정 > 관리자**로 이동하십시오.

5. **조치 메뉴**에서 **사용자 및 그룹 지정 > Windows 및 Active Directory에서**를 선택하십시오.

6. Hyper-V 그룹을 추가하십시오. 

## 폴더 권한 부여
{: #granting-folder-permissions}

Hyper-V 그룹은 Hyper-V를 원격으로 관리할 수 있는 완전한 권한을 가지고 있으므로 `C:\Users\Public\Documents\Hyper-V` 폴더에 쓸 수 있는 권한을 제공해야 합니다.

1. 내 컴퓨터를 열고 다음 주소로 이동하십시오. 

`\HOSTNAME\c$\Users\Public\Documents`

2. **Hyper-V > 특성 > 보안**으로 이동하십시오. 

3. Hyper-V 그룹을 추가하고 이 디렉토리에서 파일을 읽고 쓰고 실행하는 기능이 있는지 확인하십시오. 일반적으로 **전체 제어**를 지정하는 것이 더 쉽습니다.

## 구성 완료
{: #finalizing-configuration}

모든 구성 변경사항이 완료되었습니다. 구성을 완성하려면 Hyper-V 서버를 다시 시작해야 합니다. 서버가 다시 온라인 상태가 되면 로컬 Hyper-v Manager에서 이에 연결하십시오. 이제 모든 VM 및 Hyper-V 서비스를 관리하기 위한 전체 액세스를 가집니다.
