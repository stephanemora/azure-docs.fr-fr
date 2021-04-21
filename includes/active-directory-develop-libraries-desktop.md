---
ms.openlocfilehash: 9ddc240257bda29d5db5cae3eb6a830273d339cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590036"
---
| Langage/framework | Projet sur<br/>GitHub                                                                                     | Package                                                                               | Bien démarrer<br/>démarré                        | Connexion des utilisateurs                                         | Accès aux API web                                                 | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | Préversion publique                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [MSAL pour iOS et macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Didacticiel](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Didacticiel](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Didacticiel](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/