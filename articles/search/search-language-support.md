---
title: 영어가 아닌 검색 쿼리에 대 한 다중 언어 인덱싱
titleSuffix: Azure Cognitive Search
description: 다국어 콘텐츠를 지 원하는 인덱스를 만든 다음 해당 콘텐츠로 범위가 지정 된 쿼리를 만듭니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801607"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Azure Cognitive Search에서 여러 언어에 대 한 인덱스를 만드는 방법

다국어 검색 애플리케이션의 핵심 요구 사항은 사용자의 언어로 검색하고 결과를 볼 수 있는 기능입니다. Azure Cognitive Search에서 다국어 앱의 언어 요구 사항을 충족 하는 한 가지 방법은 특정 언어로 문자열을 저장 하기 위한 전용 필드를 만든 다음 쿼리 시 전체 텍스트 검색을 해당 필드로 제한 하는 것입니다.

+ 필드 정의에서 대상 언어의 언어 규칙을 호출 하는 언어 분석기를 설정 합니다. 지원 되는 분석기의 전체 목록을 보려면 [언어 분석기 추가](index-add-language-analyzers.md)를 참조 하세요.

+ 쿼리 요청에서 매개 변수를 설정 하 여 전체 텍스트 검색 범위를 특정 필드로 지정 하 고, 제공 하려는 검색 환경과 호환 되는 콘텐츠를 제공 하지 않는 필드의 결과를 자릅니다.

이 기술의 성공은 필드 콘텐츠의 무결성에 달려 있습니다. Azure Cognitive Search는 문자열을 변환 하거나 쿼리 실행의 일부로 언어 검색을 수행 하지 않습니다. 필드에 예상하는 문자열이 포함되어 있는지 확인하는 것은 사용자의 몫입니다.

## <a name="define-fields-for-content-in-different-languages"></a>콘텐츠 필드를 다양한 언어로 정의

Azure Cognitive Search에서 쿼리는 단일 인덱스를 대상으로 합니다. 단일 검색 환경에서 언어별 문자열을 제공하려는 개발자는 일반적으로 값을 저장하는 전용 필드(영어 문자열 필드 하나, 프랑스 문자열 필드 하나 등)를 정의합니다.

필드 정의의 "analyzer" 속성은 [언어 분석기](index-add-language-analyzers.md)를 설정 하는 데 사용 됩니다. 인덱싱 및 쿼리 실행에 모두 사용 됩니다.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>인덱스 빌드 및 로드

중간(아마도 명백한) 단계에서 쿼리를 작성하기 전에 [인덱스를 빌드하고 채워야 합니다](search-get-started-dotnet.md). 이 단계는 완전성을 위해 언급하는 것입니다. 인덱스 가용성을 확인 하는 한 가지 방법은 [포털](https://portal.azure.com)의 인덱스 목록을 확인 하는 것입니다.

> [!TIP]
> 언어 검색 및 텍스트 변환은 [AI 보강](cognitive-search-concept-intro.md) 및 [기술력과](cognitive-search-working-with-skillsets.md)를 통해 데이터를 수집 하는 동안 지원 됩니다. Azure 데이터 원본에 혼합 언어 콘텐츠가 있는 경우 [데이터 가져오기 마법사](cognitive-search-quickstart-blob.md)를 사용 하 여 언어 검색 및 번역 기능을 사용해 볼 수 있습니다.

## <a name="constrain-the-query-and-trim-results"></a>쿼리 제한 및 결과 자르기

쿼리의 매개 변수는 검색을 특정 필드로 제한한 다음 시나리오에 도움이 되지 않는 필드의 결과를 자르는 데 사용됩니다. 

| 매개 변수 | 용도 |
|-----------|--------------|
| **searchFields** | 전체 텍스트 검색을 명명된 필드 목록으로 제한합니다. |
| **$select** | 지정하는 필드만 포함하도록 응답을 잘라냅니다. 기본적으로 검색 가능한 모든 필드가 반환됩니다. **$select** 매개 변수를 통해 반환할 필드를 선택할 수 있습니다. |

프랑스어 문자열을 포함하는 필드로 검색을 제한하려면 **searchFields** 를 사용하여 해당 언어의 문자열이 포함된 필드로 쿼리의 대상을 지정합니다.

쿼리 요청에 분석기를 지정 하는 것은 필요 하지 않습니다. 필드 정의의 언어 분석기는 쿼리를 처리 하는 동안 항상 사용 됩니다. 여러 언어 분석기를 호출 하는 여러 필드를 지정 하는 쿼리의 경우 각 필드에 대해 할당 된 분석기를 사용 하 여 용어 또는 구가 독립적으로 처리 됩니다.

기본적으로 검색은 검색 가능으로 표시된 모든 필드를 반환합니다. 따라서 제공하려는 언어별 검색 환경을 따르지 않는 필드는 제외하려고 할 수 있습니다. 특히 프랑스어 문자열이 있는 필드로 검색을 제한하면 영어 문자열이 있는 필드를 결과에서 제외할 수 있습니다. **$select** 쿼리 매개 변수를 사용하면 호출 애플리케이션에 반환되는 필드를 제어할 수 있습니다.

#### <a name="example-in-rest"></a>REST의 예제

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>C의 예제 #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>언어별 필드 향상

간혹 쿼리를 실행한 에이전트의 언어를 알지 못할 수 있습니다. 이 경우 모든 필드에 대해 동시에 쿼리를 실행할 수 있습니다. 특정 언어의 결과에 대 한 IA 기본 설정은 [점수 매기기 프로필](index-add-scoring-profiles.md)을 사용 하 여 정의할 수 있습니다. 아래 예제에서 영어의 설명에 있는 일치 항목은 다른 언어의 일치 항목에 비해 더 높은 점수를 받습니다.

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

그런 다음 검색 요청에 점수 매기기 프로필을 포함 합니다.

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>다음 단계

+ [언어 분석기](index-add-language-analyzers.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [문서 검색 REST API](/rest/api/searchservice/search-documents)
+ [AI 보강 개요](cognitive-search-concept-intro.md)
+ [기술력과 개요](cognitive-search-working-with-skillsets.md)