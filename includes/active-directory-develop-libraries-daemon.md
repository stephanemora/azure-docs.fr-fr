---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007994"
---
| Langage/framework | Projet sur<br/>GitHub                                                                 | Package                                                                                | Bien démarrer<br/>démarré                           | Connexion des utilisateurs                                            | Accès aux API web                                                 | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Démarrage rapide](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| Nœud               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)  | [Démarrage rapide](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Démarrage rapide](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/