---
title: MultiStorageAccountCombo UI 요소
description: Azure Portal의 Microsoft.Storage.MultiStorageAccountCombo UI 요소에 대해 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: a8172b63039d2d247f30fca4099254cb8fca068e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073419"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 요소

공통 접두사로 시작하는 이름의 스토리지 계정을 여러 개 만드는 컨트롤 그룹입니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft-storage-multistorageaccountcombo.png)

## <a name="schema"></a>스키마

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>설명

- `defaultValue.prefix`의 값은 하나 이상의 정수와 연결되어 스토리지 계정 이름의 시퀀스를 생성합니다. 예를 들어 `defaultValue.prefix`가 **sa** 이고 `count`가 **2** 이면 **sa1** 및 **sa2** 스토리지 계정 이름이 생성됩니다. 생성된 스토리지 계정 이름의 고유성에 대한 유효성 검사가 자동으로 수행됩니다.
- 스토리지 계정 이름은 `count`에 따라 사전순으로 생성됩니다. 예를 들어 `count`가 10이면 스토리지 계정 이름은 두 자리 정수로 끝납니다(01, 02, 03).
- `defaultValue.prefix`의 기본값은 **null** 이고, `defaultValue.type`의 기본값은 **Premium_LRS** 입니다.
- `constraints.allowedTypes`에 지정되지 않은 형식은 숨겨지며, `constraints.excludedTypes`에 지정되지 않은 형식이 표시됩니다. `constraints.allowedTypes` 및 `constraints.excludedTypes`는 모두 선택 사항이지만 동시에 사용할 수는 없습니다.
- 스토리지 계정 이름을 생성하는 것 외에도 `count`는 요소에 적절한 승수를 설정하는 데 사용됩니다. **2** 와 같은 정적 값 또는 `[steps('step1').storageAccountCount]`와 같은 다른 요소의 동적 값을 지원합니다. 기본값은 **1** 입니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
