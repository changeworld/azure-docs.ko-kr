---
title: MongoDB에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Mongo DB에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: eae2d2adfe2cfdd7e47f2ace9ede9253003aa5bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368781"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MongoDB에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 MongoDB 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!IMPORTANT]
>ADF는 더 나은 네이티브 MongoDB 지원을 제공하는 이 새 버전의 MongoDB 커넥터를 릴리스합니다. 이전 버전과 호환성에 대해 있는 그대로 지원되는 솔루션에서 이전 버전의 MongoDB 커넥터를 사용하는 경우 [MongoDB 커넥터(레거시)](connector-mongodb-legacy.md) 문서를 참조하세요.


## <a name="supported-capabilities"></a>지원되는 기능

MongoDB 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히이 MongoDB 커넥터는 **최대 4.2 버전을** 지원 합니다.


## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 MongoDB 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.


## <a name="linked-service-properties"></a>연결된 서비스 속성

MongoDB 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type |Type 속성은 **MongoDbV2** 로 설정 해야 합니다. |예 |
| connectionString |MongoDB 연결 문자열을 지정합니다(예: `mongodb://[username:password@]host[:port][/[database][?options]]`). 자세한 내용은 [연결 문자열에 대한 MongoDB 설명서](https://docs.mongodb.com/manual/reference/connection-string/)를 참조하세요. <br/><br /> Azure Key Vault에 연결 문자열을 넣을 수도 있습니다. 자세한 내용은 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 을 참조 하세요. |예 |
| 데이터베이스 | 액세스하려는 데이터베이스 이름입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

**예:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. MongoDB 데이터 세트에 대해 다음 속성을 지원합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **MongoDbV2Collection** 로 설정 해야 합니다. | 예 |
| collectionName |MongoDB 데이터베이스에 있는 컬렉션의 이름입니다. |예 |

**예:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 MongoDB 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="mongodb-as-source"></a>MongoDB를 원본으로

복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **MongoDbV2Source** 로 설정 해야 합니다. | 예 |
| filter | 쿼리 연산자를 사용하여 선택 영역 필터를 지정합니다. 컬렉션의 모든 문서를 반환하려면 이 매개 변수를 생략하거나 빈 문서({})를 전달합니다. | 아니요 |
| cursorMethods.project | 프로젝션에 대한 문서에서 반환할 필드를 지정합니다. 일치하는 문서에서 모든 필드를 반환하려면 이 매개 변수를 생략합니다. | 아니요 |
| cursorMethods.sort | 쿼리가 일치하는 문서를 반환하는 순서를 지정합니다. [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)를 참조하세요. | 아니요 |
| cursorMethods.limit | 서버에서 반환하는 문서의 최대 수를 지정합니다. [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)를 참조하세요.  | 아니요 |
| cursorMethods.skip | MongoDB가 결과를 반환하기 시작하는 위치에서 건너뛸 문서 수를 지정합니다. [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)을 참조하세요. | 아니요 |
| batchSize | MongoDB 인스턴스의 응답을 각각 일괄 처리로 반환할 문서 수를 지정합니다. 대부분의 경우 일괄 처리 크기를 수정해도 사용자 또는 애플리케이션에 영향이 없습니다. Cosmos DB는 각 일괄 처리의 크기는 40 바이트를 초과할 수 없습니다 .이 크기는 batchSize 문서 크기의 합계 이므로 문서 크기가 크면이 값을 낮춥니다. | 아니요<br/>기본값은 **100** 입니다. |

>[!TIP]
>ADF는 **Strict 모드** 로 BSON 문서를 사용하는 것을 지원합니다. 필터 쿼리가 Shell 모드가 아닌 Strict 모드에 있는지 확인합니다. 자세한 설명은 [MongoDB 설명서](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)를 참조하세요.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="export-json-documents-as-is"></a>JSON 문서를 있는 그대로 내보내기

이 MongoDB 커넥터를 사용하면 JSON 문서를 있는 그대로 MongoDB 컬렉션에서 다양한 파일 기반 저장소로 또는 Azure Cosmos DB로 내보낼 수 있습니다. 이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "구조"(*스키마* 라고도 함) 섹션을 건너뛰세요.


## <a name="schema-mapping"></a>스키마 매핑

데이터를 MongoDB에서 테이블 형식 싱크로 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
