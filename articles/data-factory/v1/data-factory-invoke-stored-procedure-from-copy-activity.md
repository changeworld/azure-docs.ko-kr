---
title: Azure Data Factory 복사 작업에서 저장 프로시저 호출
description: Azure SQL Database 또는 Azure Data Factory 복사 작업의 SQL Server에서 저장 프로시저를 호출하는 방법을 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f06b84ac0807a37c7adc603a557894be85a4cea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374969"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Azure Data Factory의 복사 작업에서 저장 프로시저 호출
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory에서 저장 프로시저 작업을 사용하여 데이터 변환](../transform-data-using-stored-procedure.md)을 참조하세요.


[SQL Server](data-factory-sqlserver-connector.md) 또는 [Azure SQL Database](data-factory-azure-sql-connector.md)로 데이터를 복사할 때 복사 작업에 저장 프로시저를 호출할 **SqlSink** 를 구성할 수 있습니다. 대상 테이블에 데이터를 삽입하기 전에 추가 처리(열 병합, 값 검색, 여러 테이블에 삽입 등)를 수행하는 저장 프로시저를 사용할 수 있습니다. 이 기능은 [테이블 값 매개 변수](/dotnet/framework/data/adonet/sql/table-valued-parameters)을 활용합니다. 

다음 샘플에서는 Data Factory 파이프라인(복사 작업)에서 SQL Server Database의 저장 프로시저를 호출하는 방법을 보여 줍니다.  

## <a name="output-dataset-json"></a>출력 데이터 세트 JSON
출력 데이터 세트 JSON에서 **형식** 을 **SqlServerTable** 로 설정합니다. **AzureSqlTable** 로 설정 하 여 Azure SQL Database에 사용 합니다. **tableName** 속성 값은 저장 프로시저의 첫 번째 매개 변수 이름과 일치해야 합니다.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>복사 작업 JSON의 SqlSink 섹션
복사 작업 JSON의 **SqlSink** 섹션을 다음과 같이 정의합니다. 데이터를 싱크/대상 데이터베이스에 삽입하는 동안 저장 프로시저를 호출하려면 **SqlWriterStoredProcedureName** 및 **SqlWriterTableType** 속성 모두에 값을 지정합니다. 이러한 속성에 대한 설명은 [SQL Server 커넥터 문서의 SqlSink 섹션](data-factory-sqlserver-connector.md#sqlsink)을 참조하세요.

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>저장 프로시저 정의 
데이터베이스에서 **SqlWriterStoredProcedureName** 와 동일한 이름을 사용 하 여 저장 프로시저를 정의 합니다. 저장 프로시저는 원본 데이터 저장소의 입력 데이터를 처리하고 데이터를 대상 데이터베이스의 테이블에 삽입합니다. 저장 프로시저의 첫 번째 매개 변수 이름은 데이터 세트 JSON(Marketing)에 정의된 tableName과 일치해야 합니다.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>테이블 유형 정의
데이터베이스에서 **sqlwritertabletype과** 와 동일한 이름을 사용 하 여 테이블 형식을 정의 합니다. 테이블 형식의 스키마는 입력 데이터 세트의 스키마와 일치해야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>다음 단계
완전한 JSON 예제에 대한 다음 커넥터 문서를 검토합니다. 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)