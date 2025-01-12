---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007983"
---
| Dil/çerçeve | Proje açık<br/>GitHub                                                                                     | Paket                                                                               | Almanızı<br/>başlama                        | Oturum açma kullanıcıları                                         | Web API 'Lerine erişin                                                 | Genel olarak kullanılabilir (GA) *veya*<br/>Genel Önizleme<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Tron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | Genel Önizleme                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| macOS (Swift/obj-C)  | [iOS ve macOS için MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Öğretici](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Öğretici](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Öğretici](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![Kitaplık, Kullanıcı oturum açma için KIMLIK belirteçleri isteyebilir.][y] | ![Kitaplık, korumalı Web API 'Leri için erişim belirteçleri isteyebilir.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure önizlemeleri için ek kullanım koşulları,][preview-tos] kitaplıklar için *genel önizlemede* geçerlidir.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/