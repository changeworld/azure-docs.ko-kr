---
title: '빠른 시작: Azure CLI를 사용하여 Azure API for FHIR 배포'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 Azure API for FHIR을 배포하는 방법을 알아봅니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10bc0ac3a61ddba22dea46dfdd47a0305e2d9149
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018648"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure API for FHIR 배포

이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 Azure API for FHIR을 배포하는 방법을 알아봅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>HealthcareAPI 확장 추가

```azurecli-interactive
az extension add --name healthcareapis
```

HealthcareAPI에 대한 명령 목록을 가져옵니다.

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Azure Resource 그룹 만들기

Azure API for FHIR을 포함할 리소스 그룹의 이름을 선택하고 만듭니다.

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Azure API for FHIR 배포

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Fetch FHIR API 기능 문

다음을 사용하여 FHIR API에서 기능 문을 가져옵니다.

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Azure API for FHIR을 구독에 배포했습니다. Azure API for FHIR에서 추가 설정을 설정하려면 추가 설정 방법 가이드로 이동합니다. Azure API for FHIR 사용을 시작할 준비가 되면 애플리케이션을 등록하는 방법에 대해 자세히 읽어보세요.

>[!div class="nextstepaction"]
>[Azure API for FHIR의 추가 설정](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[애플리케이션 등록 개요](fhir-app-registration.md)
