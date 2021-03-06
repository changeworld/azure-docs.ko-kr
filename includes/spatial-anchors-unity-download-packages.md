---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076708"
---
다음은 Unity용 Azure Spatial Anchors 패키지를 다운로드하는 단계입니다. 

> [!WARNING]
> ASA SDK 2.7.0은 지원되는 최소 버전입니다. Unity 2020을 사용하는 경우 지원되는 최소 버전은 ASA SDK 2.9.0입니다.

Unity에서 Azure Spatial Anchors를 사용하려면 지원할 계획인 각 플랫폼(Android/iOS/HoloLens)에 대한 플랫폼별 패키지 뿐만 아니라 코어 패키지를 다운로드해야 합니다.

| 플랫폼 | 패키지 이름                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<version_number> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |

# <a name="download-with-web-browser"></a>[웹 브라우저를 사용하여 다운로드](#tab/unity-package-web-ui)

[여기](https://aka.ms/aoa/unity-sdk/package)에서 Unity용 Azure Spatial Anchors 코어 패키지를 찾습니다. 원하는 버전을 선택하고 **다운로드** 단추를 사용하여 패키지를 다운로드합니다. 이 단계를 반복하여 지원할 계획인 각 플랫폼에 대한 패키지를 다운로드합니다.

# <a name="download-with-npm"></a>[NPM을 사용하여 다운로드](#tab/unity-package-npm)

이 단계를 수행하려면 <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a>이 설치되어 있고 사용이 가능해야 합니다.

다음 명령을 실행하여 `<version_number>`를 현재 폴더에 다운로드할 Azure Spatial Anchors 버전으로 바꿉니다.

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> 사용 가능한 Azure Spatial Anchors 패키지 버전을 나열하려면 다음을 실행합니다.
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Azure Spatial Anchors 코어 패키지는 명령을 실행한 폴더에 다운로드됩니다. 이 단계를 반복하여 지원할 계획인 각 플랫폼에 대한 패키지를 다운로드합니다.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Mixed Reality 기능 도구(베타)를 사용하여 설치](#tab/unity-package-mixed-reality-feature-tool)

다음 단계를 계속 진행합니다. 이후 단계에서 <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality 기능 도구</a>를 사용합니다.

---