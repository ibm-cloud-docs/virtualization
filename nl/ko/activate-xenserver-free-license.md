---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer 활성화
{: #activating-xenserver}

Citrix XenServer 무료 라이센스는 초기 설치 또는 다시 로드한 후에 30일 동안 라이센스가 부여됩니다. 이 시간 동안에는 Citrix로 등록해야 하며 완전한 1년 라이센스를 요청해야 합니다. 이 라이센스를 작성하려면 Citrix에 {{site.data.keyword.cloud}}에서 액세스 권한을 갖고 있지 않은 개인 정보가 필요합니다. 라이센스는 XenServer를 사용하기 위한 사용자와 Citrix 간의 계약입니다. {:shortdesc}

30일 내에 시스템을 등록하지 않으면 가상 머신의 전원을 켤 수 없습니다.
{:tip}

시스템을 등록하려면 다음 단계를 완료하십시오. 

1. XenCenter 클라이언트를 다운로드하여 설치하십시오. [Citrix XenCenter ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://community.citrix.com/display/xs/XenCenter){: new_window} 페이지에서 클라이언트에 액세스할 수 있습니다. 

2. VPN을 통해 사설 네트워크에 연결하십시오.

3. XenCenter를 시작하고 **새 서버 추가**를 클릭하거나 드롭 다운 메뉴에서 **서버 > 추가...**를 클릭하십시오. 

4. **호스트 이름**에 사설 IP 주소를, **사용자 이름**에 'root'를, **비밀번호**에 루트 비밀번호를 사용하십시오.

5. 시스템이 연결되고 나면 대화 상자가 표시됩니다. 이 대화 상자는 라이센스가 30일 후에 만료됨을 경고합니다. 드롭 다운 메뉴에서 대화 상자가 표시되지 않으면 **도구 > 라이센스 관리자...**로 이동하십시오. 

6. 서버를 검사하고 **활성화...**를 클릭하십시오. 브라우저가 열리고 Citrix 등록 사이트로 이동합니다. 양식을 완료하고 제출하십시오. Citrix에서 이메일에 대한 받은 편지함을 확인하십시오. 이 이메일에는 첨부 파일로 라이센스 키가 있습니다.

7. 이메일을 받고 나면 첨부된 라이센스 키 파일을 로컬로 저장하십시오. 

8. XenCenter 왼쪽 창에서 서버를 강조표시하십시오. 메뉴 표시줄에서 **서버 > 라이센스 키 설치...**로 이동하십시오. 라이센스 키의 위치를 묻는 대화 상자가 나타납니다. 7단계에서 저장된 위치로 이를 가리키십시오. 
