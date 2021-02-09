---
title: Bibliothèques d’authentification de la plateforme d’identités Microsoft | Azure
description: Liste des bibliothèques clientes et des intergiciels (middleware) compatibles avec la plateforme d’identités Microsoft. Utilisez ces bibliothèques pour ajouter la prise en charge de la connexion des utilisateurs (authentification) et de l’accès (autorisation) aux API web protégées à vos applications.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218273"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliothèques d’authentification de plateforme d’identité Microsoft

Les tableaux suivants présentent la prise en charge de la bibliothèque d’authentification Microsoft pour plusieurs types d’applications. Ils incluent des liens vers le code source de la bibliothèque, où se procurer le package du projet de votre application et si la bibliothèque prend en charge la connexion des utilisateurs (authentification), l’accès aux API web protégées (autorisation) ou les deux.

La plateforme d’identités Microsoft a été certifiée par OpenID Foundation en tant que [fournisseur OpenID certifié](https://openid.net/certification/). Si vous préférez utiliser une bibliothèque autre que la bibliothèque d’authentification Microsoft (MSAL) ou une autre bibliothèque prise en charge par Microsoft, choisissez-en une avec une [implémentation OpenID Connect certifiée](https://openid.net/developers/certified/).

Si vous choisissez de coder manuellement votre propre implémentation de niveau protocole de [OAuth 2.0 ou OpenID Connect 1.0](active-directory-v2-protocols.md), portez une attention particulière aux considérations de sécurité dans les spécifications de chaque standard et suivez une méthodologie de cycle de vie de développement logiciel (SDL) telle que [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Application monopage (SPA)

Une application monopage s’exécute entièrement sur la surface du navigateur et récupère les données de page (HTML, CSS et JavaScript) de manière dynamique ou au moment du chargement de l’application. Elle peut appeler des API web pour interagir avec des sources de données principales.

Étant donné que le code d’une application monopage s’exécute entièrement dans le navigateur, il est considéré comme un *client public* qui ne peut pas stocker les secrets de façon sécurisée.

| Langage/framework | Projet sur<br/>GitHub                                                                                                    | Package                                                                      | Bien démarrer<br/>démarré                             | Connexion des utilisateurs                                         | Accès aux API web                                                 | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | Préversion publique                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Didacticiel](tutorial-v2-angular.md)              | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | Préversion publique                                               |
| JavaScript           | [MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Didacticiel](tutorial-v2-javascript-auth-code.md) | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | Préversion publique                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

## <a name="web-application"></a>Application web

Une application web exécute du code sur un serveur qui génère et envoie du code HTML, CSS et JavaScript au navigateur web d’un utilisateur pour qu’il soit affiché. L’identité de l’utilisateur est conservée en tant que session entre le navigateur de l’utilisateur (front-end) et le serveur web (back-end).

Étant donné que le code d’une application web s’exécute sur le serveur web, il est considéré comme un *client confidentiel* qui peut stocker les secrets de façon sécurisée.

| Langage/framework | Projet sur<br/>GitHub                                                                                     | Package                                                                                                    | Bien démarrer<br/>démarré                               | Connexion des utilisateurs                                            | Accès aux API web                                                    | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y]    | GA                                                           |
| ASP.NET Core         | [Sécurité ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y]    | ![La bibliothèque ne peut pas demander des jetons d’accès pour les API web protégées.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y]    | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Démarrage rapide](quickstart-v2-java-webapp.md)        | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y]    | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y]    | GA                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                                                | [Démarrage rapide](quickstart-v2-nodejs-webapp-msal.md) | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y]    | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y]    | Préversion publique                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [passport-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [Démarrage rapide](quickstart-v2-nodejs-webapp.md)      | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y]    | ![La bibliothèque ne peut pas demander des jetons d’accès pour les API web protégées.][n] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Démarrage rapide](quickstart-v2-python-webapp.md)      | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y]    | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

## <a name="desktop-application"></a>Application de bureau

Une application de bureau est généralement du code binaire (compilé) qui couvre une interface utilisateur et qui est conçu pour s’exécuter sur le Bureau d’un utilisateur.

Étant donné qu’une application de bureau s’exécute sur le Bureau de l’utilisateur, elle est considérée comme un *client public* qui ne peut pas stocker les secrets de façon sécurisée.

| Langage/framework | Projet sur<br/>GitHub                                                                                     | Package                                                                               | Bien démarrer<br/>démarré                        | Connexion des utilisateurs                                         | Accès aux API web                                                 | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | Préversion publique                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [MSAL pour iOS et macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Didacticiel](tutorial-v2-ios.md)             | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Didacticiel](tutorial-v2-windows-uwp.md)     | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Didacticiel](tutorial-v2-windows-desktop.md) | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

## <a name="mobile-application"></a>Application mobile

Une application mobile est généralement du code binaire (compilé) qui couvre une interface utilisateur et qui est conçu pour s’exécuter sur l’appareil mobile d’un utilisateur.

Étant donné qu’une application mobile s’exécute sur l’appareil mobile de l’utilisateur, elle est considérée comme un *client public* qui ne peut pas stocker les secrets de façon sécurisée.

| Plateforme          | Projet sur<br/>GitHub                                                                          | Package                                                                               | Bien démarrer<br/>démarré                    | Connexion des utilisateurs                                         | Accès aux API web                                                 | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Démarrage rapide](quickstart-v2-android.md) | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| iOS (Swift/Obj-C) | [MSAL pour iOS et macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Didacticiel](tutorial-v2-ios.md)         | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![La bibliothèque peut demander des jetons d’ID pour la connexion de l’utilisateur.][y] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

## <a name="service--daemon"></a>Service/démon

Les services et démons sont couramment utilisés pour la communication de serveur à serveur et d’autres communications sans assistance (parfois appelées *sans périphérique de contrôle*). Étant donné qu’il n’existe aucun utilisateur au clavier pour entrer des informations d’identification ou donner son consentement à l’accès aux ressources, ces applications s’authentifient en leur nom, et non en tant qu’utilisateur, lors de la demande d’un accès autorisé aux ressources d’une API web.

Un service ou un démon qui s’exécute sur un serveur est considéré comme un *client confidentiel* qui peut stocker ses secrets de façon sécurisée.

| Langage/framework | Projet sur<br/>GitHub                                                                 | Package                                                                                | Bien démarrer<br/>démarré                           | Connexion des utilisateurs                                            | Accès aux API web                                                 | Disponibilité générale *ou*<br/>Préversion publique<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Démarrage rapide](quickstart-v2-netcore-daemon.md) | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Démarrage rapide](quickstart-v2-python-daemon.md)  | ![La bibliothèque ne peut pas demander des jetons d’ID pour la connexion de l’utilisateur.][n] | ![La bibliothèque peut demander des jetons d’accès pour les API web protégées.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Les conditions d’utilisation supplémentaires des préversions Microsoft Azure][preview-tos] s’appliquent aux bibliothèques en *préversion publique*.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la bibliothèque d’authentification Microsoft, reportez-vous à la [vue d’ensemble de la bibliothèque d’authentification Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
