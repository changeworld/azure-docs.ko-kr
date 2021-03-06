---
title: Azure Managed Disks의 서버 쪽 암호화
description: Azure Storage는 미사용 데이터를 암호화한 후 저장소 클러스터에 보관하여 데이터를 보호합니다. 고객 관리 키를 사용 하 여 사용자 고유의 키로 암호화를 관리 하거나, Microsoft 관리 키를 사용 하 여 관리 디스크를 암호화할 수 있습니다.
author: roygara
ms.date: 03/11/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 786ecef5d62c7dd18e3992fa0b233b27a80d762b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721854"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure 디스크 저장소의 서버 쪽 암호화

대부분의 Azure 관리 디스크는 SSE (서버 쪽 암호화)를 사용 하 여 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 Azure Storage 암호화를 사용 하 여 암호화 됩니다. Azure Storage 암호화는 Azure 관리 디스크 (OS 및 데이터 디스크)에 저장 된 데이터를 클라우드에 유지할 때 기본적으로 자동으로 암호화 합니다. 그러나 호스트에서 암호화가 설정 된 디스크는 Azure Storage를 통해 암호화 되지 않습니다. 호스트에서 암호화가 설정 된 디스크의 경우 VM을 호스트 하는 서버에서 데이터에 대 한 암호화를 제공 하 고 암호화 된 데이터가 Azure Storage로 흐릅니다.

Azure Managed Disks의 데이터는 사용 가능한 가장 강력한 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 투명하게 암호화되며 FIPS 140-2 규격입니다. 암호화 모듈의 기본 Azure Managed Disks에 대한 자세한 정보는 [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)을 참조하세요.

Azure Storage 암호화는 관리 디스크의 성능에 영향을 주지 않으며 추가 비용이 없습니다. Azure Storage 암호화에 대 한 자세한 내용은 [Azure Storage 암호화](/azure/storage/common/storage-service-encryption)를 참조 하세요.

> [!NOTE]
> 호스트에서 암호화를 사용 하도록 설정 하지 않으면 임시 디스크는 관리 디스크가 아니고 SSE로 암호화 되지 않습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리형 키를 사용하여 관리 디스크를 암호화하거나 사용자 고유의 키를 사용하여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 관리 디스크의 모든 데이터를 암호화 및 암호 해독하는 데 사용할 *고객 관리형 키* 를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대한 각 옵션을 자세히 설명합니다.

### <a name="platform-managed-keys"></a>플랫폼 관리형 키

기본적으로 관리 디스크는 플랫폼 관리형 암호화 키를 사용합니다. 기존 관리 디스크에 작성 된 모든 관리 디스크, 스냅숏, 이미지 및 데이터는 플랫폼 관리 키를 사용 하 여 미사용 상태로 자동으로 암호화 됩니다.

### <a name="customer-managed-keys"></a>고객 관리형 키

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결 해야 하는 경우에는 [Azure PowerShell 모듈이](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) 나 [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)를 사용 하 여 모든 데이터를 고객이 관리 하는 키를 사용 하지 않는 완전히 다른 관리 되는 디스크에 복사 해야 합니다.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>지원되는 지역

관리 디스크를 사용할 수 있는 모든 지역에서 고객 관리 키를 사용할 수 있습니다.

자동 키 회전은 미리 보기로 제공 되며 다음 지역 에서만 사용할 수 있습니다.

- 미국 동부
- 미국 동부 2
- 미국 중남부
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽
- 프랑스 중부

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

관리 디스크에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 [Azure PowerShell 모듈](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 또는 [Azure Portal](disks-enable-customer-managed-keys-portal.md)를 사용 하 여 사용 하도록 설정 하는 방법에 대 한 문서를 참조 하세요. 자동 키 회전을 사용 하 여 고객 관리 키를 사용 하도록 설정 하는 방법을 알아보려면 [자동 키 회전 (미리 보기)을 사용 하 여 Azure Key Vault 설정 및 Disk를 설정](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview)을 참조 하세요.

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>호스트의 암호화-VM 데이터의 종단 간 암호화

호스트에서 암호화를 사용 하도록 설정 하면 vm이 할당 되는 Azure 서버인 VM 호스트 자체에서 암호화가 시작 됩니다. 임시 디스크 및 OS/데이터 디스크 캐시의 데이터는 해당 VM 호스트에 저장 됩니다. 호스트에서 암호화를 사용 하도록 설정 하 고 나면이 모든 데이터가 미사용 시 암호화 되며 저장소 서비스로 암호화 된 흐름이 유지 됩니다. 기본적으로 호스트의 암호화는 종단 간 데이터를 암호화 합니다. 호스트의 암호화는 VM의 CPU를 사용 하지 않으며 VM의 성능에 영향을 주지 않습니다. 

종단 간 암호화를 사용 하는 경우 임시 디스크와 사용 후 삭제 OS 디스크는 플랫폼 관리 키를 사용 하 여 미사용에 암호화 됩니다. OS 및 데이터 디스크 캐시는 선택한 디스크 암호화 유형에 따라 고객이 관리 하거나 플랫폼에서 관리 하는 키를 사용 하 여 미사용으로 암호화 됩니다. 예를 들어, 디스크가 고객 관리 키를 사용 하 여 암호화 된 경우 디스크의 캐시는 고객 관리 키를 사용 하 여 암호화 되 고, 디스크는 플랫폼 관리 키를 사용 하 여 암호화 되는 경우 디스크에 대 한 캐시는 플랫폼 관리 키를 사용 하 여 암호화 됩니다.

### <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

호스트에서 암호화를 사용 하 여 종단 간 암호화를 사용 하도록 설정 하려면 [Azure PowerShell 모듈](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)또는 [Azure Portal](disks-enable-host-based-encryption-portal.md)를 사용 하 여 종단 간 암호화를 사용 하도록 설정 하는 방법에 대 한 문서를 참조 하세요.

## <a name="double-encryption-at-rest"></a>휴지 상태의 이중 암호화

공격 받는 특정 암호화 알고리즘, 구현 또는 키와 관련 된 위험을 염려 하는 높은 수준의 보안 민감한 고객은 이제 플랫폼 관리 암호화 키를 사용 하 여 인프라 계층에서 다른 암호화 알고리즘/모드를 사용 하 여 추가 암호화 계층을 선택할 수 있습니다. 이 새 계층은 지속형 OS와 데이터 디스크, 스냅숏 및 이미지에 적용할 수 있으며,이는 모두 이중 암호화를 사용 하 여 미사용에서 암호화 됩니다.

### <a name="supported-regions"></a>지원되는 지역

관리 디스크를 사용할 수 있는 모든 지역에서 이중 암호화를 사용할 수 있습니다.

관리 디스크에 대 한 미사용 암호화를 사용 하도록 설정 하려면 [Azure PowerShell 모듈](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) 또는 [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)를 사용 하 여 사용 하도록 설정 하는 방법에 대 한 문서를 참조 하세요.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure Disk Encryption

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) 은 Linux의 [DM](https://en.wikipedia.org/wiki/Dm-crypt) 기능 또는 Windows의 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 기능을 활용 하 여 게스트 VM 내에서 고객 관리 키를 사용 하 여 관리 되는 디스크를 암호화 합니다.  고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.
> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell 모듈](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)또는 [Azure Portal](disks-enable-host-based-encryption-portal.md)를 사용 하 여 호스트에서 암호화를 사용 하 여 종단 간 암호화를 사용 하도록 설정 합니다.
- [Azure PowerShell 모듈](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) 또는 [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)를 사용 하 여 관리 디스크에 대해 미사용에서 이중 암호화를 사용 하도록 설정 합니다.
- [Azure PowerShell 모듈](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 또는 [Azure Portal](disks-enable-customer-managed-keys-portal.md)를 사용 하 여 관리 디스크에 대해 고객이 관리 하는 키를 사용 하도록 설정 합니다.
- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../key-vault/general/overview.md)
