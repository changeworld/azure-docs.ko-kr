---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007971"
---
| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                                     | 패키지                                                                               | 도달<br/>시작됨                        | 사용자 로그인                                         | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [iOS 및 macOS용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [자습서](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [자습서](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [자습서](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/