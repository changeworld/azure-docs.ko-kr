---
title: PowerShell을 사용 하 여 Key Vault 설정
description: PowerShell을 사용 하 여 가상 컴퓨터에 사용할 Key Vault를 설정 하는 방법
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557491"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 가상 머신에 대 한 Key Vault 설정

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager 스택에서 비밀/인증서는 주요 자격 증명 모음 리소스 공급자가 제공하는 리소스로 모델링됩니다. Key Vault에 대한 자세한 내용을 보려면 [Azure Key Vault란?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. 주요 자격 증명 모음을 Azure Resource Manager 가상 머신에서 사용하려면 주요 자격 증명에 대한 **EnabledForDeployment** 속성을 true로 설정해야 합니다. 다양한 클라이언트에서 이 작업을 수행할 수 있습니다.
> 2. 주요 자격 증명 모음은 Virtual Machine과 동일한 구독 및 위치에 만들어야 합니다.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>PowerShell을 사용하여 주요 자격 증명 모음 설정
PowerShell을 사용하여 키 자격 증명 모음을 만들려면 [PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../../key-vault/secrets/quick-create-powershell.md)을 참조하세요.

새 주요 자격 증명의 경우 다음 PowerShell cmdlet을 사용할 수 있습니다.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

기존 주요 자격 증명의 경우 다음 PowerShell cmdlet을 사용할 수 있습니다.

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>CLI를 사용하여 주요 자격 증명 모음 설정
CLI(명령줄 인터페이스)를 사용하여 주요 자격 증명 모음을 만들려면 [CLI를 사용하여 주요 자격 증명 모음 관리](../../key-vault/general/manage-with-cli2.md#create-a-key-vault)를 참조하세요.

CLI의 경우 먼저 주요 자격 증명 모음을 만든 다음 배포 정책을 할당해야 합니다. 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

그런 다음 템플릿 배포에 사용할 Key Vault를 사용 하도록 설정 하려면 다음 명령을 실행 합니다.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>템플릿을 사용하여 주요 자격 증명 모음 설정
템플릿을 사용하는 경우 `enabledForDeployment` 속성을 주요 자격 증명 모음 리소스에 대한 `true`로 설정해야 합니다.

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

템플릿을 사용 하 여 주요 자격 증명 모음을 만들 때 구성할 수 있는 다른 옵션은 [키 자격 증명 모음 만들기](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)를 참조 하세요.
