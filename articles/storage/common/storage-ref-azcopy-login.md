---
title: azcopy 로그인 | Microsoft Docs
description: 이 문서에서는 azcopy login 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878361"
---
# <a name="azcopy-login"></a>azcopy login

Azure Active Directory에 로그인 하 여 Azure Storage 리소스에 액세스 합니다.

## <a name="synopsis"></a>개요

Azure Active Directory에 로그인 하 여 Azure Storage 리소스에 액세스 합니다.

Azure Storage 계정에 대 한 권한을 부여 하려면 저장소 계정, 부모 리소스 그룹 또는 부모 구독의 컨텍스트에서 사용자 계정에 **저장소 Blob 데이터 참가자** 역할을 할당 해야 합니다.

이 명령은 OS 기본 제공 메커니즘을 사용 하 여 현재 사용자에 대 한 암호화 된 로그인 정보를 캐시 합니다.

> [!IMPORTANT]
> 명령줄을 사용 하 여 환경 변수를 설정 하는 경우 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록에서 자격 증명을 포함 하는 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않게 하려면 스크립트를 사용 하 여 사용자에 게 자격 증명을 묻는 메시지를 표시 하 고 환경 변수를 설정할 수 있습니다.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예제

일반으로 설정 된 기본 AAD 테 넌 트 ID로 대화형으로 로그인 합니다.

```azcopy
azcopy login
```

지정 된 테 넌 트 ID로 대화형으로 로그인 합니다.

```azcopy
azcopy login --tenant-id "[TenantID]"
```

VM (가상 컴퓨터)의 시스템 할당 id를 사용 하 여 로그인 합니다.

```azcopy
azcopy login --identity
```

사용자가 할당 한 VM id와 서비스 id의 클라이언트 ID를 사용 하 여 로그인 합니다.
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
VM의 사용자 할당 id와 서비스 id의 개체 ID를 사용 하 여 로그인 합니다.

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

VM의 사용자 할당 id와 서비스 id의 리소스 ID를 사용 하 여 로그인 합니다.
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

클라이언트 암호를 사용 하 여 서비스 주체로 로그인: 환경 변수 AZCOPY_SPA_CLIENT_SECRET을 비밀 기반 서비스 주체 인증에 대 한 클라이언트 암호로 설정 합니다.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

인증서 및 암호를 사용 하 여 서비스 주체로 로그인 합니다.

환경 변수 AZCOPY_SPA_CERT_PASSWORD을 인증서 기반 서비스 주체 인증에 대 한 인증서의 암호로 설정 합니다.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

`/path/to/my/cert`PEM 또는 PKCS12 파일의 경로로 처리 합니다. AzCopy는 인증서를 가져오기 위해 시스템 인증서 저장소에 도달 하지 않습니다.

`--certificate-path` 인증서 기반 서비스 주체 인증을 수행할 때 반드시 필요 합니다.

## <a name="options"></a>옵션

**--aad 끝점** 문자열 사용할 Azure Active Directory 끝점입니다. 기본값은 https://login.microsoftonline.com) 글로벌 Azure 클라우드에 대해 올바릅니다. 국가별 클라우드에서 인증할 때이 매개 변수를 설정 합니다. 관리 서비스 ID에는 필요 하지 않습니다.

**--** 사용자 할당 id의 응용 프로그램 Id 문자열 응용 프로그램 id입니다. 서비스 주체 인증에 필요 합니다.

**--certificate-** SPN 인증을 위한 인증서의 경로 문자열 경로입니다. 인증서 기반 서비스 주체 인증에 필요 합니다.

**--**   명령에 대 한 도움말 도움말 `azcopy login` 입니다.

**--id**   MSI (관리 서비스 id) 라고도 하는 가상 컴퓨터의 id를 사용 하 여 로그인 합니다.

**--id-** 사용자 할당 id의 클라이언트 Id 문자열 클라이언트 id입니다.

**--id-개체** Id 문자열 개체 id입니다.

**--id-** 사용자 할당 id의 리소스 id입니다.

**--서비스 주체**   인증서 또는 암호를 사용 하 여 SPN (서비스 사용자 이름)을 통해 로그인 합니다. 클라이언트 암호 또는 인증서 암호는 해당 환경 변수에 배치 되어야 합니다. 환경 변수의 이름 및 설명을 보려면 AzCopy env를 입력 합니다.

**--테 넌 트-id** 문자열 OAuth 장치 대화형 로그인에 사용할 Azure Active Directory 테 넌 트 id입니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps float|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|
|--신뢰할 수 있는 microsoft 접미사 문자열   |Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 '*. core.windows.net;* 입니다. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
