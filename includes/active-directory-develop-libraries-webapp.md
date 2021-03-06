---
ms.openlocfilehash: 124290321815d22cad74d235205401b9380ff395
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "104578375"
---
| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                                     | 패키지                                                                                                    | 도달<br/>시작됨                               | 사용자 로그인                                            | 웹 Api 액세스                                                    | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
| ASP.NET Core         | [ASP.NET 보안](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리가 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 없습니다.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. 웹](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. 웹](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     |[빠른 시작](../articles/active-directory/develop/quickstart-v2-java-webapp.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
| Node.js              | [MSAL 노드](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-노드](https://www.npmjs.com/package/@azure/msal-node)                                                | [빠른 시작](../articles/active-directory/develop/quickstart-v2-nodejs-webapp-msal.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [passport-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [빠른 시작](../articles/active-directory/develop/quickstart-v2-nodejs-webapp.md)      | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [빠른 시작](../articles/active-directory/develop/quickstart-v2-python-webapp.md)      | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/