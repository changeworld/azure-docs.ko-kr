---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6897bf9b4ccce71048af88a3108e3d87d17a375d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434494"
---
:::row:::
    :::column span="3":::
        Android 용 Java SDK는 필요한 라이브러리 및 필요한 Android 권한을 포함 하는 <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android 라이브러리) </a>로 패키지 됩니다. `https://csspeechstorage.blob.core.windows.net/maven/`의 Maven 리포지토리에서 패키지 `com.microsoft.cognitiveservices.speech:client-sdk:1.15.0`로 호스트됩니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Android Studio 프로젝트의 패키지를 사용하는 경우 다음과 같이 변경합니다.

1. 프로젝트 수준 *gradle* 파일에서 섹션에 다음을 추가 합니다 `repositories` .
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. 모듈 수준 *gradle* 파일에서 섹션에 다음을 추가 합니다 `dependencies` .
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
  ```

Java SDK는 [Speech Devices SDK](../speech-devices-sdk.md)에도 포함됩니다.

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 특정 Java 빠른 시작 소스 코드 </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">JRE (Java Runtime) 빠른 시작 소스 코드 </a>
