---
title: '자습서: Azure Firewall Manager를 사용하여 가상 허브 보호'
description: 이 자습서에서는 Azure Portal을 통해 Azure Firewall Manager를 사용하여 가상 허브를 보호하는 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670463"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>자습서: Azure Firewall Manager를 사용하여 가상 허브 보호

Azure Firewall Manager를 사용하여 개인 IP 주소, Azure PaaS 및 인터넷으로 향하는 클라우드 네트워크 트래픽을 보호하는 보안 가상 허브를 만들 수 있습니다. 방화벽에 대한 트래픽 라우팅이 자동화되므로 UDR(사용자 정의 경로)을 만들 필요가 없습니다.

![클라우드 네트워크 보안](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager는 허브 가상 네트워크 아키텍처도 지원합니다. 보안 가상 허브 및 허브 가상 네트워크 아키텍처 유형을 비교하려면 [Azure Firewall Manager 아키텍처 옵션이란?](vhubs-and-vnets.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 스포크 가상 네트워크 만들기
> * 보안 가상 허브 만들기
> * 허브 및 스포크 가상 네트워크 연결
> * 허브로 트래픽 라우팅
> * 서버 배포
> * 방화벽 정책 만들기 및 허브 보안 설정
> * 방화벽 테스트

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-hub-and-spoke-architecture"></a>허브 및 스포크 아키텍처 만들기

먼저, 서버를 배치할 수 있는 스포크 가상 네트워크를 만듭니다.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>스포크 가상 네트워크 및 서브넷 두 개 만들기

두 가상 네트워크 내에는 각각 워크로드 서버가 있으며 방화벽으로 보호됩니다.

1. Azure Portal 홈 페이지에서 **리소스 만들기** 를 선택합니다.
2. **가상 네트워크** 를 검색하고 **만들기** 를 선택합니다.
2. **구독** 의 경우 사용자의 구독을 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고, 이름에 **fw-manager-rg** 를 입력하고, **확인** 을 선택합니다.
2. **이름** 에 **Spoke-01** 을 입력합니다.
3. **지역** 에 대해 **(US) 미국 동부** 를 선택합니다.
4. 완료되면 **다음: IP 주소** 를 선택합니다.
1. **주소 공간** 에 **10.0.0.0/16** 을 입력합니다.
3. **서브넷 이름** 아래에서 **기본값** 을 선택합니다.
4. **서브넷 이름** 에 **Workload-01-SN** 을 입력합니다.
5. **서브넷 주소 범위** 의 경우 **10.0.1.0/24** 를 입력합니다.
6. **저장** 을 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
2. **만들기** 를 선택합니다.

이 프로세스를 반복하여 유사한 가상 네트워크를 하나 더 만듭니다.

이름: **Spoke-02**<br>
주소 공간: **10.1.0.0/16**<br>
서브넷 이름: **Workload-02-SN**<br>
서브넷 주소 범위: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>보안 가상 허브 만들기

Firewall Manager를 사용하여 보안 가상 허브를 만듭니다.

1. Azure Portal 홈 페이지에서 **모든 서비스** 를 선택합니다.
2. 검색 상자에 **Firewall Manager** 를 입력하고 **Firewall Manager** 를 선택합니다.
3. **Firewall Manager** 페이지에서 **보안 가상 허브 보기** 를 선택합니다.
4. **Firewall Manager | 보안 가상 허브** 페이지에서 **새 보안 가상 허브 만들기** 를 선택합니다.
5. **리소스 그룹** 에 대해 **fw-manager-rg** 를 선택합니다.
7. **지역** 에 대해 **미국 동부** 를 선택합니다.
1. **보안 가상 허브 이름** 에 **Hub-01** 을 입력합니다.
2. **Hub 주소 공간** 에 **10.2.0.0/16** 을 입력합니다.
3. 새 vWAN 이름에 **Vwan-01** 을 입력합니다.
4. **신뢰할 수 있는 보안 파트너를 사용하도록 설정하기 위해 VPN Gateway 포함** 확인란을 취소된 상태로 둡니다.
5. 완료되면 **다음: Azure Firewall** 을 선택합니다.
6. 기본 **Azure Firewall** **사용** 설정을 그대로 적용한 후, **다음: 신뢰할 수 있는 보안 파트너** 를 선택합니다.
7. 기본 **신뢰할 수 있는 보안 파트너** **사용 안 함** 설정을 그대로 적용하고 **다음: 리뷰 + 만들기** 를 클릭합니다.
8. **만들기** 를 선택합니다. 

   배포하는 데 약 30분이 걸립니다.

배포가 완료된 후 방화벽 공용 IP 주소를 가져올 수 있습니다.

1. **Firewall Manager** 를 엽니다.
2. **가상 허브** 를 선택합니다.
3. **hub-01** 을 선택합니다.
7. **공용 IP 구성** 을 선택합니다.
8. 나중에 사용할 공용 IP 주소를 적어둡니다.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>허브 및 스포크 가상 네트워크 연결

이제 허브 및 스포크 가상 네트워크를 피어링할 수 있습니다.

1. **fw-manager-rg** 리소스 그룹을 선택한 다음, **Vwan-01** 가상 WAN을 선택합니다.
2. **연결** 에서 **가상 네트워크 연결** 을 선택합니다.
3. **연결 추가** 를 선택합니다.
4. **연결 이름** 에 **hub-spoke-01** 을 입력합니다.
5. **허브** 에 대해 **Hub-01** 을 선택합니다.
6. **리소스 그룹** 에 대해 **fw-manager-rg** 를 선택합니다.
7. **가상 네트워크** 에 대해 **Spoke-01** 을 선택합니다.
8. **만들기** 를 선택합니다.

반복하여 **Spoke-02** 가상 네트워크에 연결합니다. 연결 이름 - **hub-spoke-02**

## <a name="deploy-the-servers"></a>서버 배포

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.
2. **인기** 목록에서 **Windows Server 2016 Datacenter** 를 선택합니다.
3. 가상 머신에 대해 다음 값을 입력합니다.

   |설정  |값  |
   |---------|---------|
   |Resource group     |**fw-manager-rg**|
   |가상 머신 이름     |**Srv-workload-01**|
   |지역     |**((미국) 미국 동부)**|
   |관리자 사용자 이름     |사용자 이름 입력|
   |암호     |암호 입력|

4. **인바운드 포트 규칙** 에서 **퍼블릭 인바운드 포트** 에 **없음** 을 선택합니다.
6. 나머지는 기본값으로 두고 **다음: 디스크** 를 선택합니다.
7. 디스크는 기본값으로 두고 **다음: 네트워킹** 을 선택합니다.
8. 가상 네트워크에 대해 **Spoke-01** 을 선택하고 서브넷에 대해 **Workload-01-SN** 을 선택합니다.
9. **공용 IP** 에 대해 **없음** 을 선택합니다.
11. 나머지는 기본값으로 두고 **다음: 관리** 를 선택합니다.
12. **사용 안 함** 을 선택하여 부팅 진단을 비활성화합니다. 나머지는 기본값으로 두고 **검토 + 만들기** 를 선택합니다.
13. 요약 페이지에서 설정을 검토한 다음, **만들기** 를 선택합니다.

다음 표의 정보를 사용하여 **Srv-Workload-02** 라는 이름의 다른 가상 머신을 구성합니다. 나머지 구성은 **Srv-workload-01** 가상 머신과 동일합니다.

|설정  |값  |
|---------|---------|
|가상 네트워크|**Spoke-02**|
|서브넷|**Workload-02-SN**|

서버가 배포된 후 서버 리소스를 선택하고 **네트워킹** 에서 각 서버의 개인 IP 주소를 기록합니다.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>방화벽 정책 만들기 및 허브 보안 설정

방화벽 정책은 하나 이상의 보안 가상 허브에서 트래픽을 전달하는 규칙 컬렉션을 정의합니다. 방화벽 정책을 만든 다음, 허브를 보호합니다.

1. Firewall Manager에서 **Azure Firewall 정책 보기** 를 선택합니다.
2. **Azure Firewall 정책 만들기** 를 선택합니다.
1. **리소스 그룹** 에 대해 **fw-manager-rg** 를 선택합니다.
1. **정책 세부 정보** 에서 **이름** 에 **Policy-01** 을 입력하고 **지역** 에 대해 **미국 동부** 를 선택합니다.
1. **다음: DNS 설정** 을 선택합니다.
1. **다음: TLS 검사(미리 보기)** 를 선택합니다.
1. **다음: 규칙** 을 선택합니다.
1. **규칙** 탭에서 **규칙 컬렉션 추가** 를 선택합니다.
1. **규칙 컬렉션 추가** 페이지에서 **이름** 에 **App-RC-01** 을 입력합니다.
1. **규칙 컬렉션 유형** 에 대해 **애플리케이션** 을 선택합니다.
1. **우선 순위** 에 대해 **100** 을 입력합니다.
1. **규칙 컬렉션 작업** 이 **허용** 인지 확인합니다.
1. 규칙 **이름** 에 **Allow-msft** 를 입력합니다.
1. **원본 유형** 에 대해 **IP 주소** 를 선택합니다.
1. **원본** 에 대해 **\*** 를 선택합니다.
1. **프로토콜** 에 **http,https** 를 입력합니다.
1. **대상 유형** 이 **FQDN** 인지 확인합니다.
1. **대상** 에 **\*microsoft.com** 을 입력합니다.
1. **추가** 를 선택합니다.

원격 데스크톱을 **Srv-Workload-01** 가상 머신에 연결할 수 있도록 DNAT 규칙을 추가합니다.

1. **추가/규칙 컬렉션** 을 선택합니다.
1. **이름** 에 **dnat-rdp** 를 입력합니다.
1. **규칙 컬렉션 형식** 에 대해 **DNAT** 를 선택합니다.
1. **우선 순위** 에 대해 **100** 을 입력합니다.
1. 규칙 **이름** 에 **Allow-rdp** 를 입력합니다.
1. **원본 유형** 에 대해 **IP 주소** 를 선택합니다.
1. **원본** 에 대해 **\*** 를 선택합니다.
1. **프로토콜** 의 경우 **TCP** 를 선택합니다.
1. **대상 포트** 에 대해 **3389** 를 입력합니다.
1. **대상 유형** 에 대해 **IP 주소** 를 선택합니다.
1. **대상** 에 이전에 적어둔 방화벽 공용 IP 주소를 입력합니다.
1. **번역된 주소** 에 이전에 적어둔 **Srv-Workload-01** 의 개인 IP 주소를 입력합니다.
1. **변환 포트** 에 **3389** 를 입력합니다.
1. **추가** 를 선택합니다.

**Srv-Workload-01** 에서 **Srv-Workload-02** 로 원격 데스크톱을 연결할 수 있도록 네트워크 규칙을 추가합니다.

1. **규칙 컬렉션 추가** 를 선택합니다.
2. **이름** 에 **vnet-rdp** 를 입력합니다.
3. **규칙 컬렉션 형식** 에 대해 **네트워크** 를 선택합니다.
4. **우선 순위** 에 대해 **100** 을 입력합니다.
1. **규칙 컬렉션 작업** 에 대해 **허용** 을 선택합니다.
1. 규칙 **이름** 에 **Allow-vnet** 을 입력합니다.
1. **원본 유형** 에 대해 **IP 주소** 를 선택합니다.
1. **원본** 에 대해 **\*** 를 선택합니다.
1. **프로토콜** 의 경우 **TCP** 를 선택합니다.
1. **대상 포트** 에 대해 **3389** 를 입력합니다.
1. **대상 유형** 에 대해 **IP 주소** 를 선택합니다.
1. **대상** 에 이전에 적어둔 **Srv-Workload-02** 개인 IP 주소를 입력합니다.
1. **추가** 를 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="associate-policy"></a>연결 정책

방화벽 정책을 허브와 연결합니다.

1. Firewall Manager에서 **Azure Firewall 정책** 을 선택합니다.
1. **Policy-01** 의 확인란을 선택합니다.
1. **연결 관리/허브 연결** 을 선택합니다.
1. **hub-01** 을 선택합니다.
1. **추가** 를 선택합니다.

## <a name="route-traffic-to-your-hub"></a>허브로 트래픽 라우팅

이제 네트워크 트래픽이 방화벽을 통해 라우팅되는지 확인해야 합니다.

1. Firewall Manager에서 **가상 허브** 를 선택합니다.
2. **Hub-01** 을 선택합니다.
3. **설정** 에서 **보안 구성** 을 선택합니다.
4. **인터넷 트래픽** 에서 **Azure Firewall** 을 선택합니다.
5. **프라이빗 트래픽** 에서 **Azure Firewall을 통해 전송** 을 선택합니다.
1. **저장** 을 선택합니다.

   경로 테이블을 업데이트하는 데 몇 분이 걸립니다.
1. 두 개의 연결이 Azure Firewall에서 인터넷 및 프라이빗 트래픽을 모두 보호하는지 확인합니다.

## <a name="test-the-firewall"></a>방화벽 테스트

방화벽 규칙을 테스트하려면 방화벽 공용 IP 주소(**Srv-Workload-01** 에 NAT로 연결됨)를 사용하여 원격 데스크톱을 연결합니다. 여기에서 브라우저를 사용하여 애플리케이션 규칙을 테스트하고 원격 데스크톱을 **Srv-Workload-02** 에 연결하여 네트워크 규칙을 테스트합니다.

### <a name="test-the-application-rule"></a>애플리케이션 규칙 테스트

이제 방화벽 규칙이 예상대로 작동하는지 테스트합니다.

1. 원격 데스크톱을 방화벽 공용 IP 주소에 연결하고 로그인합니다.

3. Internet Explorer를 열고 [https://www.google.com]\(`https://www.microsoft.com` )을 찾습니다.
4. Internet Explorer 보안 경고에서 **확인** > **닫기** 를 선택합니다.

   Microsoft 홈페이지가 표시됩니다.

5. [https://www.microsoft.com]\(`https://www.google.com`) 로 이동합니다.

   방화벽에 의해 차단됩니다.

따라서, 방화벽 애플리케이션 규칙이 작동하는지 확인했습니다.

* 다른 모든 FQDN이 아닌 허용된 FQDN 하나만 찾아볼 수 있습니다.

### <a name="test-the-network-rule"></a>네트워크 규칙 테스트

이제 네트워크 규칙을 테스트합니다.

- Srv-Workload-01에서 원격 데스크톱을 Srv-Workload-02 개인 IP 주소로 엽니다.

   원격 데스크톱이 Srv-Workload-02에 연결됩니다.

따라서, 방화벽 네트워크 규칙이 작동하는지 확인했습니다.
* 다른 가상 네트워크에 있는 서버에 원격 데스크톱을 연결할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

방화벽 리소스 테스트가 완료되면 **fw-manager-rg** 리소스 그룹을 삭제하여 모든 방화벽 관련 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰할 수 있는 보안 파트너에 대해 알아보기](trusted-security-partners.md)
