---
title: Azure Cosmos DB 쿼리 언어의 StringToNull
description: Azure Cosmos DB의 SQL 시스템 함수 StringToNull에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339585"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Null로 변환 된 식을 반환 합니다. 식을 변환할 수 없는 경우는 undefined를 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   Null 식으로 구문 분석할 문자열 식입니다.
  
## <a name="return-types"></a>반환 형식
  
  Null 식 또는 undefined를 반환 합니다.  
  
## <a name="examples"></a>예제
  
  다음 예제에서는가 `StringToNull` 서로 다른 형식에서 동작 하는 방법을 보여 줍니다. 

유효한 입력을 사용 하는 예제는 다음과 같습니다.

 공백은 "null" 전후에만 사용할 수 있습니다.

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

입력이 잘못 된 예는 다음과 같습니다.

Null은 대/소문자를 구분 하며 모든 소문자 (예: "null")로 작성 되어야 합니다.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{}]
```  

전달 된 식이 null 식으로 구문 분석 됩니다. 이러한 입력은 null 형식으로 계산 되지 않으므로 undefined로 반환 됩니다.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{}]
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
