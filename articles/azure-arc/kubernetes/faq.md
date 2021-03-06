---
title: Azure Arc 사용 Kubernetes 질문과 대답
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에는 Azure Arc enabled Kubernetes와 관련 된 질문과 대답 목록이 포함 되어 있습니다.
keywords: Kubernetes, Arc, Azure, 컨테이너, 구성, GitOps, faq
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025852"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>질문과 대답-Azure Arc 사용 Kubernetes

이 문서에서는 Azure Arc enabled Kubernetes에 대해 자주 묻는 질문과 대답을 다룹니다.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Azure Arc를 지원하는 Kubernetes 및 AKS(Azure Kubernetes Service)의 차이점은 무엇인가요?

AKS는 Azure에서 관리 되는 Kubernetes 제품입니다. AKS는 azure에 대 한 많은 복잡성 및 운영 오버 헤드를 오프 로드 하 여 Azure에서 관리 되는 Kubernetes 클러스터의 배포를 간소화 합니다. Kubernetes 마스터는 Azure에서 관리되므로 에이전트 노드만 관리하고 유지 관리합니다.

Azure Arc enabled Kubernetes를 사용 하면 Kubernetes 클러스터를 Azure에 연결 하 여 Azure의 관리 기능 (예: Azure Monitor 및 Azure Policy)을 확장할 수 있습니다. 기본 Kubernetes 클러스터 자체를 유지 관리 합니다.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Azure에서 실행 되는 AKS 클러스터를 Azure Arc에 연결 해야 하나요?

아니요. 게이트 키퍼 (Azure Monitor 및 Azure Policy)를 비롯 한 모든 Azure Arc 사용 Kubernetes 기능은 AKS (Azure Resource Manager의 네이티브 리소스)에서 사용할 수 있습니다.
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Azure Stack Azure Stack 허브에 있는 AKS-HCI 클러스터 및 Kubernetes 클러스터를 Azure Arc에 연결 해야 하나요?

예, Azure Stack Edge 또는 Azure Stack Hub에 있는 AKS-HCI 클러스터 또는 Kubernetes 클러스터를 Azure Arc에 연결 하면 Azure Resource Manager에서 리소스를 표시 하는 클러스터가 제공 됩니다. 이 리소스 표현은 클러스터 구성, Azure Monitor 및 Azure Policy (게이트 키퍼)와 같은 기능을 연결 된 Kubernetes 클러스터로 확장 합니다.

Azure Arc enabled Kubernetes 클러스터가 Edge Azure Stack에 있는 경우 AKS on Azure Stack HCI (>= 4 월 2021 업데이트) 또는 AKS on Windows Server 2019 Datacenter (>= 4 월 2021 업데이트)에서 Kubernetes 구성은 무료로 포함 됩니다.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>만료 된 Azure Arc 활성화 된 Kubernetes 리소스를 해결 하는 방법

Azure Arc enabled Kubernetes와 연결 된 MSI (관리 서비스 ID) 인증서의 만료 기간은 90 일입니다. 이 인증서가 만료 되 면 리소스가 고려 되며 `Expired` 모든 기능 (예: 구성, 모니터링 및 정책)이이 클러스터에서 작동 하지 않습니다. Kubernetes 클러스터에서 Azure Arc를 다시 사용 하려면 다음을 수행 합니다.

1. 클러스터에서 Azure Arc enabled Kubernetes 리소스 및 에이전트를 삭제 합니다. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. 클러스터에 에이전트를 배포 하 여 Azure Arc enabled Kubernetes 리소스를 다시 만듭니다.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` 는 또한 클러스터의 위쪽에서 구성을 삭제 합니다. 을 실행 한 후 `az connectedk8s connect` 수동으로 또는 Azure Policy를 사용 하 여 클러스터에 구성을 다시 만듭니다.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>이미 CI/CD 파이프라인을 사용 하 고 있는 경우 Azure Arc enabled Kubernetes 및 구성을 계속 사용할 수 있나요?

예, CI/CD 파이프라인을 통해 배포를 수신 하는 클러스터에서 구성을 계속 사용할 수 있습니다. 기존 CI/CD 파이프라인과 비교할 때 구성 기능에는 다음과 같은 두 가지 추가 이점이 있습니다.

**드리프트 조정**

CI/CD 파이프라인은 파이프라인 실행 중에 한 번만 변경 내용을 적용 합니다. 그러나 클러스터의 GitOps 운영자는 Git 리포지토리를 지속적으로 폴링하여 클러스터에 있는 Kubernetes 리소스의 원하는 상태를 인출 합니다. GitOps 연산자가 필요한 리소스 상태를 클러스터에 있는 리소스의 실제 상태와 다르게 찾으면이 드리프트가 조정 됩니다.

**대규모로 GitOps 적용**

CI/CD 파이프라인은 Kubernetes 클러스터에 대 한 이벤트 기반 배포 (예: Git 리포지토리에 푸시)에 유용 합니다. 그러나 모든 Kubernetes 클러스터에 동일한 구성을 배포 하려는 경우에는 CI/CD 파이프라인에 각 Kubernetes 클러스터의 자격 증명을 수동으로 구성 해야 합니다. 

Azure Arc enabled Kubernetes의 경우 Azure Resource Manager에서 구성을 관리 하므로 구독 또는 리소스 그룹의 범위 내에서 Azure Policy를 사용 하 여 모든 Azure Arc enabled Kubernetes 리소스에 대해 동일한 구성 만들기를 자동화할 수 있습니다. 이 기능은 정책 할당 후에 생성 된 Azure Arc enabled Kubernetes 리소스에도 적용 됩니다.

이 기능은 규정 준수 및 거 버 넌 스 요구 사항을 충족 하기 위해 전체 Kubernetes 클러스터 인벤토리에 있는 기준 구성 (예: 네트워크 정책, 역할 바인딩 및 pod 보안 정책)을 적용 합니다.

## <a name="next-steps"></a>다음 단계

* [Kubernetes 클러스터를 Azure Arc에 연결 하는](./quickstart-connect-cluster.md)빠른 시작을 안내 합니다.
* Kubernetes 클러스터에 연결 된 Azure Arc가 이미 있나요? [Arc Enabled Kubernetes 클러스터에 대 한 구성을 만듭니다](./tutorial-use-gitops-connected-cluster.md).
* Azure Policy를 사용 하 여 [대규모 구성을 적용](./use-azure-policy.md)하는 방법을 알아봅니다.