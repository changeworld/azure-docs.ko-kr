---
title: Event Grid 소스로 Azure 앱 구성
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure 앱 구성을 사용 하는 방법을 설명 합니다. 스키마와 자습서 및 방법 문서 링크를 제공합니다.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366775"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Event Grid 소스로 Azure 앱 구성
이 문서에서는 Azure 앱 구성 이벤트에 대 한 속성 및 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 Azure 앱 구성을 이벤트 원본으로 사용 하는 빠른 시작 및 자습서의 목록을 제공 합니다.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure 앱 구성은 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| KeyValueModified | 키-값이 만들어지거나 바뀔 때 발생 합니다. |
| Microsoft AppConfiguration. KeyValueDeleted | 키 값이 삭제 될 때 발생 합니다. |

## <a name="example-event"></a>예제 이벤트

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
다음 예에서는 키-값 수정 된 이벤트의 스키마를 보여 줍니다. 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

키-값이 삭제 된 이벤트에 대 한 스키마는 다음과 유사 합니다. 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예에서는 키-값 수정 된 이벤트의 스키마를 보여 줍니다. 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

키-값이 삭제 된 이벤트에 대 한 스키마는 다음과 유사 합니다. 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | 개체 | 앱 구성 이벤트 데이터입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |


# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | 개체 | 앱 구성 이벤트 데이터입니다. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

데이터 개체의 속성은 다음과 같습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| `key` | 문자열 | 수정 되거나 삭제 된 키-값의 키입니다. |
| `label` | 문자열 | 수정 되거나 삭제 된 키-값의 레이블 (있는 경우)입니다. |
| `etag` | 문자열 | `KeyValueModified`새 키 값의 etag에 대 한입니다. `KeyValueDeleted`삭제 된 키-값의 etag에 대 한입니다. |


## <a name="tutorials-and-how-tos"></a>자습서 및 방법

|제목 | 설명 |
|---------|---------|
| [Event Grid를 사용 하 여 Azure 앱 구성 이벤트에 대응](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid와 Azure 앱 구성을 통합 하는 방법에 대 한 개요입니다. |
| [데이터 변경 알림에 Event Grid 사용](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure 앱 구성 이벤트 구독을 사용 하 여 웹 끝점에 키-값 수정 이벤트를 보내는 방법에 대해 알아봅니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Azure 앱 구성 이벤트를 사용 하는 방법에 대 한 소개는 [데이터 변경 알림에 Event Grid 사용](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)을 참조 하세요. 