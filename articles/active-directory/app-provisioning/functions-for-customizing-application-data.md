---
title: Azure Active Directory에서 특성 매핑에 대 한 식 작성에 대 한 참조
description: Azure Active Directory에서 SaaS 앱 개체의 자동화된 프로비전 중 허용되는 형식으로 특성 값을 변환하기 위해 식 매핑을 사용하는 방법에 대해 알아봅니다. 에는 함수에 대 한 참조 목록이 포함 되어 있습니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0334f52b87071c8f363a0dfcc793170316747096
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198509"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Azure AD에서 특성 매핑에 대 한 식 작성에 대 한 참조

SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 경우, 사용자의 데이터를 SaaS 응용 프로그램에 보다 적합 한 형식으로 변환할 수 있는 스크립트와 유사한 식을 작성 해야 합니다.

## <a name="syntax-overview"></a>구문 개요

특성 매핑을 위한 식의 구문은 VBA(Visual Basic Applications) 함수를 연상시킵니다.

* 전체 식은 이름 앞에 괄호로 구성 된 함수를 사용 하 여 정의 해야 합니다 .이 함수는 *FunctionName ( `<<argument 1>>` , `<<argument N>>` )* 의 인수로 구성 됩니다.
* 서로 함수를 중첩할 수 있습니다. 예:  *Functionone (Functionone ( `<<argument1>>` ))*
* 함수에 3가지 다른 유형의 인수를 전달할 수 있습니다.
  
  1. 특성은 대괄호로 묶어야 합니다. 예: [attributeName]
  2. 문자열 상수는 큰따옴표로 묶어야 합니다. 예: "미국"
  3. 기타 함수 예: FunctionOne ( `<<argument1>>` , functionone ( `<<argument2>>` ))
* 문자열 상수의 경우, 백슬래시 (\) 또는 따옴표(")가 문자열에 필요한 경우 백슬래시(\) 기호로 이스케이프되어야 합니다. 예: "회사 이름: \\ " Contoso \\ ""
* 구문은 대/소문자를 구분 합니다. 함수 vs 복사는 여기에서 직접 붙여 넣는 것입니다. 

## <a name="list-of-functions"></a>함수 목록

[](#append) &nbsp; &nbsp; 추가 &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; Bitand &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; Cbool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; 병합 &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [](#count) &nbsp; &nbsp; 개수 &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; Formatdatetime &nbsp; &nbsp; [](#guid) &nbsp; &nbsp; Guid &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIF &nbsp; &nbsp; [](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; Isstring &nbsp; &nbsp; [](#item) &nbsp; &nbsp; 항목 &nbsp; &nbsp; [](#join) &nbsp; &nbsp; 조인 &nbsp; &nbsp; [](#left) &nbsp; &nbsp; 왼쪽 &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [NumFromDate](#numfromdate) &nbsp; &nbsp; &nbsp; &nbsp; [removeduplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [singleapproleassignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [분할](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Append

**함수:** 추가 (원본, 접미사)

**설명:** 소스 문자열 값을 사용 하 여 접미사의 끝에 접미사를 추가 합니다.

**변수의**

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **접미사** |필수 |String |원본 값의 끝에 추가하려는 문자열입니다. |


### <a name="append-constant-suffix-to-user-name"></a>사용자 이름에 상수 접미사 추가
예: Salesforce 샌드박스를 사용 하는 경우 동기화 하기 전에 모든 사용자 이름에 추가 접미사를 추가 해야 할 수 있습니다.

**식** 
`Append([userPrincipalName], ".test")`

**샘플 입/출력:** 

* **입력**: (userPrincipalName): “John.Doe@contoso.com”
* **출력**: " John.Doe@contoso.com.test "


---
### <a name="bitand"></a>BitAnd
**함수:** BitAnd (value1, value2)

**설명:** 이 함수는 두 매개 변수를 모두 이진 표현으로 변환 하 고 비트를로 설정 합니다.

- 0 - value1 및 value2의 해당 비트 중 하나 또는 둘 모두가 0인 경우
- 1 - 2개 모두 해당 비트일 경우 1입니다.

즉, 두 매개 변수의 해당 비트가 1일 경우를 제외하는 모든 경우에는 0을 반환합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **value1** |필수 |num |Value2로 AND'ed 해야 하는 숫자 값입니다.|
| **value2** |필수 |num |Value1으로 AND'ed 해야 하는 숫자 값입니다.|

**예 들어**
`BitAnd(&HF, &HF7)`

11110111 및 00000111 = 00000111 `BitAnd` 은 이진값 00000111의 이진 값인 7을 반환 합니다.

---
### <a name="cbool"></a>CBool
**칩셋용으로** 
`CBool(Expression)`

**설명:**  
 `CBool` 계산 된 식을 기반으로 부울을 반환 합니다. 식이 0이 아닌 값으로 계산 되는 경우는 `CBool` *True* 를 반환 하 고 그렇지 않으면 *False* 를 반환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **expression** |필수 | expression | 모든 유효한 식 |

**예:** 
`CBool([attribute1] = [attribute2])`                                                                    
두개의 속성이 같은 동일한 값을 가지면 True로 반환합니다.

---
### <a name="coalesce"></a>Coalesce
**함수:** 병합 (source1, 소스 2, ..., defaultValue)

**설명:** NULL이 아닌 첫 번째 소스 값을 반환 합니다. 모든 인수가 NULL 이면 defaultValue가 있는 경우 defaultValue가 반환 됩니다. 모든 인수가 NULL이 고 defaultValue가 없으면 병합은 NULL을 반환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source1  … sourceN** | 필수 | String |필수, 가변적인 횟수 대개는 원본 개체의 특성 이름입니다. |
| **defaultValue** | 선택 | 문자열 | 모든 원본 값이 NULL 일 때 사용할 기본값입니다. 빈 문자열("")일 수 있습니다.

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>NULL이 아닌 경우 흐름 메일 값, 그렇지 않으면 flow userPrincipalName
예: 메일 특성이 있는 경우이 특성을 전달 하려는 경우 그렇지 않으면 userPrincipalName의 값을 대신 전달 합니다.

**식** 
`Coalesce([mail],[userPrincipalName])`

**샘플 입/출력:** 

* **입력** (메일): NULL
* **INPUT** (userPrincipalName): " John.Doe@contoso.com "
* **출력**: " John.Doe@contoso.com "


---
### <a name="converttobase64"></a>ConvertToBase64
**함수:** ConvertToBase64 (원본)

**설명:** ConvertToBase64 함수는 문자열을 유니코드 base64 문자열로 변환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |64 기본으로 변환 될 문자열|

**예 들어**
`ConvertToBase64("Hello world!")`

"SABlAGwAbABvACAAdwBvAHIAbABkACEA"를 반환합니다.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**함수:** ConvertToUTF8Hex (원본)

**설명:** ConvertToUTF8Hex 함수는 문자열을 UTF8 16 진수 인코딩 값으로 변환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |UTF8 Hex로 변환할 문자열|

**예 들어**
`ConvertToUTF8Hex("Hello world!")`

48656C6C6F20776F726C6421을 반환합니다.

---
### <a name="count"></a>개수
**함수:** Count (특성)

**설명:** Count 함수는 다중값 특성의 요소 수를 반환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **특성도** |필수 |특성 |요소가 계산 될 다중값 특성|

---
### <a name="cstr"></a>CStr
**함수:** CStr (값)

**설명:** CStr 함수는 값을 문자열 데이터 형식으로 변환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **value** |필수 | 숫자, 참조 또는 부울 | 숫자 값, 참조 특성 또는 부울입니다. |

**예 들어**
`CStr([dn])`

"Cn = Joe, dc = contoso, dc = com"을 반환 합니다.

---
### <a name="datefromnum"></a>DateFromNum
**함수:** DateFromNum (값)

**설명:** DateFromNum 함수는 AD의 날짜 형식 값을 DateTime 형식으로 변환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **value** |필수 | 날짜 | DateTime 형식으로 변환 될 광고 날짜 |

**예 들어**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

오전 11 시에서 2012 년 1 월 1 일을 나타내는 날짜/시간을 반환 합니다.

---
### <a name="formatdatetime"></a>FormatDateTime
**함수:** FormatDateTime (source, dateTimeStyles, inputFormat, outputFormat)

**설명:** 한 형식의 날짜 문자열을 사용 하 여 다른 형식으로 변환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **dateTimeStyles** | 선택 | 문자열 | 일부 날짜 및 시간 구문 분석 메서드에 대해 문자열 구문 분석을 사용자 지정 하는 형식 지정 옵션을 지정 하려면이 옵션을 사용 합니다. 지원 되는 값은 [DateTimeStyles doc](/dotnet/api/system.globalization.datetimestyles)를 참조 하세요. 비워 두면 사용 되는 기본값은 DateTimeStyles. Datetimestyles.roundtripkind, DateTimeStyles, Numberstyles.allowleadingwhite, DateTimeStyles입니다.  |
| **inputFormat** |필수 |String |원본 값의 예상된 형식입니다. 지원 되는 형식은 [/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings)를 참조 하세요. |
| **outputFormat** |필수 |String |출력 날짜의 형식입니다. |



### <a name="output-date-as-a-string-in-a-certain-format"></a>특정 형식에서 문자열로 출력 날짜
예: 특정 형식의 ServiceNow와 같은 SaaS 응용 프로그램에 날짜를 전송 하려고 합니다. 다음 식을 사용 하 여 고려할 수 있습니다. 

**식** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**샘플 입/출력:**

* **입력** (extensionAttribute1): "20150123105347.1Z"
* **출력**: "2015-01-23"


---
### <a name="guid"></a>Guid
**함수:** Guid ()

**설명:** 함수 Guid는 새 임의 GUID를 생성 합니다.

---
### <a name="iif"></a>IIF
**함수:** IIF (condition, valueIfTrue, valueIfFalse)

**설명:** IIF 함수는 지정 된 조건에 따라 가능한 값의 집합 중 하나를 반환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **조건** |필수 |변수 또는 식 |true 또는 false로 평가될 수 있는 값 또는 식입니다. |
| **valueIfTrue** |필수 |변수 또는 문자열 | 조건이 true로 평가되는 경우 반환된 값입니다. |
| **valueIfFalse** |필수 |변수 또는 문자열 |조건이 false로 평가되는 경우 반환된 값입니다.|

**예 들어**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**함수:** InStr (value1, value2, start, compareType)

**설명:** InStr 함수는 문자열에서 맨 처음 발견 되는 부분 문자열을 찾습니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **value1** |필수 |String |검색할 문자열입니다. |
| **value2** |필수 |String |찾을 문자열 |
| **start** |선택 |정수 |하위 문자열을 찾을 시작 위치입니다.|
| **compareType** |선택 사항 |열거형 |VbTextCompare 또는 Vbtextcompare 일 수 있습니다. |

**예 들어**
`InStr("The quick brown fox","quick")`

5로 평가 됩니다.

`InStr("repEated","e",3,vbBinaryCompare)`

7로 계산합니다.

---
### <a name="isnull"></a>IsNull
**함수:** IsNull (식)

**설명:** 식이 Null로 계산 되 면 IsNull 함수는 true를 반환 합니다. 특성이 없는 경우 Null로 표현됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **expression** |필수 |expression |평가할 식입니다. |

**예 들어**
`IsNull([displayName])`

특성이 없으면 True를 반환 합니다.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**함수:** IsNullOrEmpty (식)

**설명:** 식이 null 또는 빈 문자열이 면 IsNullOrEmpty 함수는 true를 반환 합니다. 특성이 없거나, 빈 문자열로 존재하는 경우 True로 계산합니다.
이 함수의 역원을 IsPresnt라고 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **expression** |필수 |expression |평가할 식입니다. |

**예 들어**
`IsNullOrEmpty([displayName])`

특성이 없거나 빈 문자열인 경우 True를 반환 합니다.

---
### <a name="ispresent"></a>IsPresent
**함수:** IsPresent (식)

**설명:** 식이 Null이 아니고 비어 있지 않은 문자열로 평가 되는 경우 IsPresent 함수는 true를 반환 합니다. 이 함수의 역함수는 IsNullOrEmpty으로 지칭됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **expression** |필수 |expression |평가할 식입니다. |

**예 들어**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**함수:** IsString (식)

**설명:** 식을 문자열 형식으로 평가할 수 있는 경우 IsString 함수는 True로 평가 됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **expression** |필수 |expression |평가할 식입니다. |

---
### <a name="item"></a>항목
**함수:** Item (특성, 인덱스)

**설명:** Item 함수는 다중값 문자열/특성에서 하나의 항목을 반환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **특성도** |필수 |특성 |검색할 다중 값 특성 |
| **index** |필수 |정수 | 다중값 문자열의 항목에 대 한 인덱스|

**예:** 
 `Item([proxyAddresses], 1)` 다중값 특성의 두 번째 항목을 반환 합니다.

---
### <a name="join"></a>참여
**함수:** Join (separator, source1, 소스 2, ...)

**설명:** Join ()은 여러 **소스** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외 하 고 Append ()와 유사 합니다. 각 값은 **구분 기호** 문자열로 구분 됩니다.

원본 값 중 하나가 다중 값 특성인 경우, 해당 특성의 모든 값은 함께 조인되며 구분 기호 값에 의해 구분됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **separator** |필수 |String |문자열이 하나의 문자열로 연결되면 원본 값을 구분하는데 문자열을 사용합니다. 구분 기호가 필요하지 않은 경우 ""일 수 있습니다. |
| **source1  … sourceN** |필수, 시간 변수 |String |값이 함께 조인될 문자열입니다. |

---
### <a name="left"></a>왼쪽
**함수:** Left (String, NumChars)

**설명:** Left 함수는 문자열의 왼쪽부터 지정 된 수의 문자를 반환 합니다. numChars = 0 인 경우, 빈 문자열을 반환합니다.
numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
문자열이 null이면 빈 문자열을 반환합니다.
문자열이 numChars 내에서 숫자가 지정한 문자보다 적은 문자를 포함하는 경우, 문자열과 동일한 문자열(즉, 매개 변수 1의 모든 문자가 포함)이 반환됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **String** |필수 |특성 | 문자를 반환할 문자열입니다. |
| **NumChars** |필수 |정수 | 문자열의 시작 (왼쪽)부터 반환할 문자 수를 나타내는 숫자입니다.|

**예 들어**
`Left("John Doe", 3)`

"Joh"를 반환합니다.

---
### <a name="mid"></a>Mid
**함수:** Mid (원본, 시작, 길이)

**설명:** 소스 값의 부분 문자열을 반환 합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |일반적으로 특성 이름입니다. |
| **start** |필수 |정수 |하위 문자열이 시작되어야 하는 **source** 문자열의 인덱스입니다. 문자열의 첫번째 문자에는 인덱스 1이 있고, 두번째 문자에는 인덱스 2가 있습니다. |
| **length** |필수 |정수 |부분 문자열의 길이입니다. 길이가 **원본** 문자열 외부에서 종료되면 함수는 **시작** 인덱스부터 **원본** 문자열 끝까지의 부분 문자열을 반환합니다. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**함수:** NormalizeDiacritics (원본)

**설명:** 문자열 인수가 하나 필요 합니다. 문자열을 반환하지만 해당하는 비분음 부호 문자로 대체된 분음 부호 문자를 포함합니다. 일반적으로 분음 부호 문자(악센트 부호)를 포함하는 이름 및 성을 다양한 사용자 ID(예: 사용자 계정 이름, SAM 계정 이름 및 이메일 주소)에 사용할 수 있는 올바른 값으로 변환하는 데 사용됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String | 일반적으로 이름 또는 성 특성입니다. |


### <a name="remove-diacritics-from-a-string"></a>문자열에서 분음 부호 제거
예: 강조 표시를 포함 하는 문자를 악센트 부호를 포함 하지 않는 동등한 문자로 바꾸어야 합니다.

**식:** NormalizeDiacritics ([givenName])

**샘플 입/출력:** 

* **입력** (givenName): "Zoë"
* **출력**: "Zoe"


---
### <a name="not"></a>Not
**함수:** Not (원본)

**설명:** **소스의** 부울 값을 대칭 이동 합니다. **Source** 값이 True 이면 False를 반환 합니다. 그렇지 않으면 True를 반환 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |부울 문자열 |필요한 **source** 값은 "True"나 "False"입니다. |

---
### <a name="numfromdate"></a>NumFromDate
**함수:** NumFromDate (값)

**설명:** NumFromDate 함수는 날짜/시간 값을 [Accountexpires](/windows/win32/adschema/a-accountexpires)와 같은 특성을 설정 하는 데 필요한 Active Directory 형식으로 변환 합니다. 이 함수를 사용 하 여 Workday 및 SuccessFactors와 같은 클라우드 HR 앱에서 받은 DateTime 값을 동등한 AD 표현으로 변환 합니다. 

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **value** |필수 | String | 지원 되는 형식의 날짜/시간 문자열입니다. 지원되는 형식은 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx를 참조하세요. |

**예제:**
* Workday 예는 *ContractEndDate* 특성을 사용 하는 *2020-12-31-08:00* WORKDAY의 특성을 AD의 *Accountexpires* 필드에 매핑하려고 한다고 가정 하 고,이 함수를 사용 하 여 로캘과 일치 하도록 표준 시간대 오프셋을 변경 하는 방법을 보여 줍니다. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors 예를 들어 SuccessFactors에서 *M/d/yyyy hh: mm: ss tt* 형식의 *ENDDATE* 특성을 AD의 *accountexpires* 필드로 매핑하려는 경우,이 함수를 사용 하 여 로캘에 맞게 표준 시간대 오프셋을 변경 하는 방법은 다음과 같습니다.
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**함수:** RemoveDuplicates (특성)

**설명:** RemoveDuplicates 함수는 다중값 문자열을 사용 하 여 각 값이 고유한 지 확인 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **특성도** |필수 |다중값 특성 |중복 항목을 제거 하는 다중값 특성|

**예:** 
 `RemoveDuplicates([proxyAddresses])` 모든 중복 값이 제거 된 삭제 된 proxyAddress 특성을 반환 합니다.

---
### <a name="replace"></a>바꾸기
**함수:** Replace (source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**설명:** 대/소문자를 구분 하는 방식으로 문자열 내의 값을 바꿉니다. 함수는 제공 된 매개 변수에 따라 다르게 동작 합니다.

* **oldValue** 및 **replacementValue** 가 제공된 경우:
  
  * **원본** 에서 모든 **OldValue** 항목을 **replacementValue** 로 대체 합니다.
* **oldValue** 및 **template** 가 제공된 경우:
  
  * **template** 의 모든 **oldValue** 를 **source** 값으로 바꿉니다.
* **regexPattern** 및 **replacementValue** 가 제공되는 경우:

  * 함수에서 **regexPattern** 을 **source** 문자열에 적용하고, regex 그룹 이름을 사용하여 **replacementValue** 에 대한 문자열을 생성할 수 있습니다.
* **regexPattern**, **regexGroupName**, **replacementValue** 가 제공되는 경우:
  
  * 함수에서 **regexPattern** 을 **source** 문자열에 적용하고, **regexGroupName** 과 일치하는 모든 값을 **replacementValue** 로 바꿉니다.
* **regexPattern**, **regexGroupName**, **replacementAttributeName** 이 제공되는 경우:
  
  * **source** 에 값이 없는 경우 **source** 가 반환됩니다.
  * **source** 에 값이 있는 경우 함수에서 **regexPattern** 을 **source** 문자열에 적용하고, **regexGroupName** 과 일치하는 모든 값을 **replacementAttributeName** 과 연결된 값으로 바꿉니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |일반적으로 **소스** 개체의 특성 이름입니다. |
| **oldValue** |선택 |문자열 |**원본** 또는 **템플릿** 에서 대체될 값입니다. |
| **regexPattern** |선택 |문자열 |**source** 에서 바뀔, 값의 Regex 패턴입니다. 또는 **replacementPropertyName** 을 사용하는 경우 **replacementPropertyName** 에서 값을 추출하는 패턴입니다. |
| **regexGroupName** |선택 |문자열 |**regexPattern** 내 그룹의 이름입니다. **replacementPropertyName** 을 사용하는 경우에만 **replacementPropertyName** 에서 이 그룹의 값을 **replacementValue** 로 추출합니다. |
| **replacementValue** |선택 |문자열 |이전 값과 대체할 새로운 값입니다. |
| **replacementAttributeName** |선택 |문자열 |대체 값에 사용할 특성의 이름입니다. |
| **할당량** |선택 |문자열 |**템플릿** 값이 제공 되 면 템플릿 내에서 **oldValue** 를 찾아 **원본** 값으로 바꿉니다. |

### <a name="replace-characters-using-a-regular-expression"></a>정규식을 사용하여 문자를 바꿈
예: 정규식 값과 일치 하는 문자를 찾아 제거 해야 합니다.

**식** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**샘플 입/출력:**

* **INPUT** (mailNickname: "john_doe72"
* **출력**: "72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**함수:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**설명:** 식을 사용 하 여 정의 된 고유한 값 생성 규칙 인 두 개의 인수가 필요 합니다. 함수는 각 규칙을 평가한 후 대상 앱/디렉터리에서 생성된 값이 고유한지 확인합니다. 발견된 첫 번째 고유한 값이 반환됩니다. 모든 값이 이미 대상에 있는 경우 항목은 위탁되고 감사 로그에 이유가 기록됩니다. 제공할 수 있는 인수 수에 상한은 없습니다.


 - 최상위 레벨 함수이므로 중첩할 수 없습니다.
 - 우선 순위가 일치하는 특성에는 이 함수를 적용할 수 없습니다.     
 - 이 함수는 항목 만들기에만 사용할 수 있습니다. 특성과 함께 사용할 경우 **매핑 적용** 속성을 **개체를 만드는 동안만** 으로 설정합니다.
 - 이 함수는 현재 "Workday to Active Directory 사용자 프로 비전" 및 "SuccessFactors to Active Directory User 프로 비전"에만 지원 됩니다. 다른 프로비저닝 애플리케이션에서는 사용할 수 없습니다. 


**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2개 이상 필요, 상한 없음 |String | 평가할 고유한 값 생성 규칙 목록입니다. |

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>UPN(userPrincipalName) 특성의 고유한 값 생성
예: 사용자의 이름, 중간 이름 및 성에 따라 upn 특성에 값을 할당 하기 전에 UPN 특성에 대 한 값을 생성 하 고 대상 AD 디렉터리에서 고유성을 확인 해야 합니다.

**식** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**샘플 입/출력:**

* (PreferredFirstName) **입력**: “John”
* (PreferredLastName) **입력**: “Smith”
* **출력**: " John.Smith@contoso.com " UPN 값이 John.Smith@contoso.com 디렉터리에 아직 없는 경우
* **출력**: " J.Smith@contoso.com " UPN 값이 John.Smith@contoso.com 디렉터리에 이미 있는 경우
* **출력**: " Jo.Smith@contoso.com " 위의 두 UPN 값이 디렉터리에 이미 있는 경우



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**함수:** SingleAppRoleAssignment ([appRoleAssignments])

**설명:** 지정 된 응용 프로그램에 대해 사용자에 게 할당 된 모든 appRoleAssignment의 목록에서 단일 Approleassignment를 반환 합니다. 이 함수는 appRoleAssignments 개체를 단일 역할 이름 문자열로 변환하는 데 필요합니다. 모범 사례는 한 번에 하나의 appRoleAssignment만 하나의 사용자에게 할당되도록 하는 것이고 여러 역할이 할당된 경우에는 반환된 역할 문자열을 예측할 수 없습니다. 

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |필수 |String |**[appRoleAssignments]** 개체. |

---
### <a name="split"></a>분할
**함수:** Split (원본, 구분 기호)

**설명:** 지정 된 구분 기호 문자를 사용 하 여 문자열을 다중값 배열로 분할 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |업데이트할 **source** 값입니다. |
| **문자가** |필수 |String |문자열을 분할하는 데 사용할 문자(예: ",")를 지정 |

### <a name="split-a-string-into-a-multi-valued-array"></a>문자열을 다중 값 배열로 분할
예: 쉼표로 구분 된 문자열 목록을 가져와서 Salesforce의 기능 집합 특성과 같은 다중 값 특성에 연결 될 수 있는 배열로 분할 해야 합니다. 이 예의 경우 Azure AD에서 사용 권한 세트의 목록을 extensionAttribute5에 채웠습니다.

**식:** Split ([extensionAttribute5], ",")

**샘플 입/출력:** 

* **INPUT** (extensionAttribute5): ""
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**함수:** StripSpaces (원본)

**설명:** 소스 문자열에서 모든 공백 ("") 문자를 제거 합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |업데이트할 **source** 값입니다. |

---
### <a name="switch"></a>스위치
**함수:** Switch (source, defaultValue, key1, value1, key2, value2, ...)

**설명:** **원본** 값이 **키** 와 일치 하면는 해당 **키** 에 대 한 **값** 을 반환 합니다. **source** 값이 아무 키와도 일치하지 않는 경우 **defaultValue** 를 반환합니다.  **Key** 및 **value** 매개 변수는 항상 쌍으로 제공되어야 합니다. 함수는 항상 짝수 개수의 매개 변수를 예상합니다. 관리자와 같은 참조 특성에는 함수를 사용 하면 안 됩니다. 

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |**Source** 값입니다. |
| **defaultValue** |선택 |문자열 |원본이 모든 키와 일치하지 않는 경우 사용할 기본값입니다. 빈 문자열("")일 수 있습니다. |
| **key** |필수 |String |**원본** 값과 비교할 **Key** 입니다. |
| **value** |필수 |String |키와 일치하는 **원본** 의 대체 값입니다. |

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>미리 정의된 옵션 집합을 기반으로 값 바꾸기
예: Azure AD에 저장 된 상태 코드를 기반으로 사용자의 표준 시간대를 정의 해야 합니다.  상태 코드가 미리 정의된 옵션 중 하나와 일치하지 않으면 기본값인 "오스트레일리아/시드니"를 사용합니다.

**식** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**샘플 입/출력:**

* **입력** (상태): "QLD"
* **출력**: "오스트레일리아/브리즈번"


---
### <a name="tolower"></a>ToLower
**함수:** ToLower (소스, 문화권)

**설명:** 지정 된 문화권 규칙을 사용 하 여 *소스* 문자열 값을 사용 하 여 소문자로 변환 합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름 |
| **culture** |선택 |문자열 |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2* 이며, 여기서 *languagecode2* 는 2자 언어 코드이고 *country/regioncode2* 는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>생성된 userPrincipalName(UPN) 값을 소문자로 변환
예: PreferredFirstName 및 PreferredLastName 원본 필드를 연결 하 고 모든 문자를 소문자로 변환 하 여 UPN 값을 생성 하려고 합니다. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**샘플 입/출력:**

* (PreferredFirstName) **입력**: “John”
* (PreferredLastName) **입력**: “Smith”
* **출력**: " john.smith@contoso.com "


---
### <a name="toupper"></a>ToUpper
**함수:** ToUpper (소스, 문화권)

**설명:** *소스* 문자열 값을 사용 하 여 지정 된 문화권 규칙을 사용 하 여 대문자로 변환 합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **culture** |선택 |문자열 |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2* 이며, 여기서 *languagecode2* 는 2자 언어 코드이고 *country/regioncode2* 는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

---
### <a name="word"></a>Word
**함수:** Word (문자열, WordNumber, 구분 기호)

**설명:** Word 함수는 사용할 구분 기호 및 반환할 단어 번호를 설명 하는 매개 변수를 기반으로 하 여 문자열 내에 포함 된 단어를 반환 합니다. 구분 기호 내의 문자 중 하나로 구분되는 전체 문자열의 각 문자열은 단어로 식별됩니다.

숫자가 < 1인경우 , 빈 문자열을 반환합니다.
문자열이 null이면, 빈 문자열을 반환합니다.
문자열이 단어 수보다 적거나, 구분 기호로 식별되는 단어를 포함할 경우, 빈 문자열이 반환됩니다.

**변수의** 

| Name | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **String** |필수 |다중값 특성 |단어를 반환할 문자열입니다.|
| **WordNumber** |필수 | 정수 | 반환할 단어 번호를 식별 하는 번호|
| **구분 기호** |필수 |String| 단어를 식별 하는 데 사용 해야 하는 구분 기호를 나타내는 문자열입니다.|

**예 들어**
`Word("The quick brown fox",3," ")`

"갈색"을 반환 합니다.

`Word("This,string!has&many separators",3,",!&#")`

"있음"을 반환 합니다.

---

## <a name="examples"></a>예제
이 단원에서는 더 많은 식 함수 사용 예제를 제공 합니다. 

### <a name="strip-known-domain-name"></a>알려진 도메인 이름 제거
사용자 이름을 가져오려면 사용자의 전자 메일에서 알려진 도메인 이름을 제거 해야 합니다.  예를 들어, 도메인이 "contoso.com"인 경우 다음 식을 사용할 수 있습니다.

**식** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**샘플 입출력:** 

* **입력**(메일): “john.doe@contoso.com”
* **출력**: "john. doe"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>이름과 성의 부분을 연결하여 사용자 별칭을 생성합니다.
사용자의 이름 중 처음 3개 문자 및 사용자 성의 처음 5개 문자를 사용하여 사용자 별칭을 생성해야 합니다.

**식** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**샘플 입/출력:** 

* **입력** (givenName): "John"
* **입력** (surname): "Doe"
* **출력**: "JohDoe"


## <a name="related-articles"></a>관련 문서
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](../app-provisioning/user-provisioning.md)
* [사용자 프로 비전을 위한 특성 매핑 사용자 지정](../app-provisioning/customize-application-attributes.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [계정 프로 비전 알림](../app-provisioning/user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
