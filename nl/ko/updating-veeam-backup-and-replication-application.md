---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Veeam Backup & Replication 애플리케이션 업데이트
{: #updating-veeam-backup-and-replication-application}

## 개요
{: #overview}

제품의 새 업데이트를 사용할 수 있을 때 서버에서 실행 중인 Veeam Backup and Replication 애플리케이션을 업데이트하려면 다음 단계를 사용하십시오.

### 전제조건
{: #prerequisites-updating-veeam-backup-and-replication-application}

* 무료 Veeam 계정. [Veeam 등록 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.veeam.com/signin.html)에
가입할 수 있습니다.
* Internet Explorer, Chrome, Firefox 또는 Safari.

### 업데이트 확인
{: #checking-for-updates}

Veeam Backup and Replication 애플리케이션에는 업데이트 확인 프로그램이 포함되어 있습니다. 업데이트에 대한 알림을 받았는지 확인하려면 왼쪽 상단 메뉴 아이콘으로 이동하여 **일반 옵션** > **알림**을 클릭하십시오. **정기적으로 제품 및 하이퍼바이저 업데이트 확인**이 선택되어 있는지 확인하십시오.

### Veeam 업데이트
{: #updating-veeam}

업데이트를 시작하려면 다음 단계를 수행하십시오.
1. **백업 인프라** 보기를 여십시오.
2. 인벤토리 분할창에서 **관리 서버 노드** > **누락된 업데이트**로 이동하십시오.
3. 업데이트 링크를 클릭하십시오.

업데이트를 시작하기 전에 참고할 몇 가지 사항은 다음과 같습니다.

* 기존의 모든 작업에 대해 현재 실행이 완료되었는지 확인하십시오. 일부 작업이 실패한 경우 실패한 작업을 다시 실행하십시오.
* 복원 세션, 인스턴트 VM 복구 세션 및 SureBackup 작업을 포함하여 실행 중인 작업이 없는지 확인하십시오. 실행 중인 작업은 중지하지 않는 것이 좋습니다.
* 주기적 백업 복사 작업을 임시로 사용하지 않도록 설정하여 업그레이드 중에 시작되지 않도록 하십시오. 

### 다음 단계 
{: #next-steps-updating-veeam-backup-and-replication-application}

이제 다운로드된 업데이트를 추출하고 Veeam Update Installer를 두 번 클릭할 수 있습니다. 업데이트는 Veeam 에이전트가 켜져 있는 하이퍼바이저및 로컬 Veeam 서버 둘 다에 적용됩니다. 프로세스가 완료되고 나면 Veeam Backup and Replication을 시작하고 백업 작업을 다시 사용으로 설정하십시오. 
