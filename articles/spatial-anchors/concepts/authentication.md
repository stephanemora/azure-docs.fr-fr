---
title: Authentification et autorisation
description: Découvrez les différentes façons par lesquelles une application ou un service peut s’authentifier auprès d’Azure Spatial Anchors, ainsi que les niveaux de contrôle dont vous disposez pour réguler l’accès à Azure Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 133b565bc54feaf49a2fec9dd0056ca8e7ef43f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857722"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autorisation et authentification auprès d’Azure Spatial Anchors

Dans cette section, nous allons aborder les différentes façons de s’authentifier auprès d’Azure Spatial Anchors à partir de votre application ou service web, et comment utiliser le contrôle d’accès en fonction du rôle dans Azure Directory (Azure AD) pour contrôler l’accès à vos comptes Spatial Anchors.

## <a name="overview"></a>Vue d’ensemble

![Diagramme montrant une vue d’ensemble de l’authentification auprès d’Azure Spatial Anchors.](./media/spatial-anchors-authentication-overview.png)

Pour accéder à un compte Azure Spatial Anchors donné, les clients doivent d’abord obtenir un jeton d’accès à partir du service d’émission de jeton de sécurité (STS, Security Token Service) Azure Mixed Reality. Les jetons obtenus à partir du service STS ont une durée de vie de 24 heures, et contiennent des informations permettant aux services Spatial Anchors de prendre des décisions d’autorisation sur le compte. Ils garantissent que seuls les principaux autorisés peuvent accéder à ce compte.

Les jetons d’accès peuvent être obtenus en échange d’une clé de compte ou à partir de jetons émis par AD Azure.

Les clés de compte vous permettent de commencer à utiliser rapidement le service Azure Spatial Anchors. Toutefois, avant de déployer votre application en production, nous vous recommandons de mettre à jour votre application afin qu’elle utilise l’authentification basée sur Azure AD.

Les jetons d’authentification Azure AD peuvent être obtenus de deux façons :

- Si vous générez une application d’entreprise et que votre société utilise Azure AD comme système d’identité, vous pouvez utiliser l’authentification Azure AD basée sur l’utilisateur dans votre application, et accorder l’accès à vos comptes Spatial Anchors à l’aide de vos groupes de sécurité Azure AD existants, ou directement aux utilisateurs de votre organisation.
- Sinon, nous vous recommandons d’obtenir des jetons Azure AD à partir d’un service web prenant en charge votre application. Le recours à un service web de prise en charge est la méthode d’authentification recommandée pour les applications de production, car elle évite de devoir incorporer les informations d’identification pour l’accès à Azure Spatial Anchors dans votre application cliente.

## <a name="account-keys"></a>Clés de compte

Le recours à des clés de compte pour l’accès à votre compte Azure Spatial Anchors est la façon la plus simple de bien démarrer. Vous trouverez vos clés de compte dans le portail Azure. Accédez à votre compte, puis sélectionnez l’onglet « Clés ».

![Capture d’écran montrant la page « Clés » avec le bouton « Copier » de la « Clé primaire » mis en évidence.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Deux clés sont mises à disposition. Toutes deux sont simultanément valides pour l’accès au compte Spatial Anchors. Nous vous recommandons de mettre régulièrement à jour la clé que vous utilisez pour accéder au compte. Le fait d’avoir deux clés valides distinctes permet d’effectuer ces mises à jour sans temps d’arrêt ; il vous suffit de mettre alternativement à jour la clé primaire et la clé secondaire.

Le SDK offre une prise en charge intégrée de l’authentification avec des clés de compte. Vous devez simplement définir la propriété AccountKey sur votre objet cloudSession.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Une fois cette opération effectuée, le SDK gère l’échange de la clé de compte contre un jeton d’accès, et la mise en cache nécessaire des jetons pour votre application.

> [!WARNING]
> L’utilisation de clés de compte est recommandée pour l’intégration rapide, mais uniquement durant le développement/prototypage. Nous vous déconseillons vivement de ne pas livrer votre application en production avec une clé de compte incorporée, mais d’adopter plutôt les approches d’authentification Azure AD basées sur le service ou l’utilisateur listée ci-dessous.

## <a name="azure-ad-user-authentication"></a>Authentification utilisateur Azure AD

Pour les applications ciblant les utilisateurs Azure Active Directory, l’approche recommandée consiste à utiliser un jeton Azure AD pour l’utilisateur, que vous pouvez obtenir à l’aide de la [bibliothèque MSAL](../../active-directory/develop/msal-overview.md). Vous devez suivre les étapes indiquées dans le [guide de démarrage rapide Inscrire une application](../../active-directory/develop/quickstart-register-app.md), notamment :

1. Configuration dans le portail Azure
    1.    Inscrivez votre application dans Azure AD en tant qu’**Application native**. Dans le cadre de l’inscription, vous devez déterminer si votre application doit être multilocataire ou non, et fournir les URL de redirection autorisées pour votre application.
        1.  Basculer sur l’onglet **Autorisations de l’API**
        2.  Sélectionner **Ajouter une autorisation**
            1.  Sélectionner **Fournisseur de ressources Azure Mixed Reality** sous l’onglet **API utilisées par mon organisation**
            2.  Sélectionner **Autorisations déléguées**
            3.  Cochez la case **mixedreality.signin** sous **mixedreality**
            4.  Sélectionner **Ajouter des autorisations**
        3.  Sélectionner **Accorder le consentement administrateur**
    2.    Accordez à votre application ou aux utilisateurs l’accès à votre ressource :
        1.    Accédez à votre ressource Spatial Anchors dans le portail Azure.
        2.    Accédez à l’onglet **Contrôle d’accès (IAM)** .
        3.    Cliquez sur **Ajouter une attribution de rôle**.
            1.    [Sélectionnez un rôle](#role-based-access-control).
            2.    Dans le champ **Sélectionner**, entrez le nom des utilisateurs, groupes et/ou applications auxquels vous souhaitez accorder l’accès.
            3.    Cliquez sur **Enregistrer**.
2. Dans votre code :
    1.    Veillez à utiliser l’**ID d’application** et l’**URI de redirection** de votre propre application Azure AD comme paramètres d’**ID client** et d’**Uri de redirection** dans la bibliothèque MSAL
    2.    Définissez les informations sur le locataire :
        1.    Si votre application prend en charge **Mon organisation uniquement**, remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire** (par exemple contoso.microsoft.com).
        2.    Si votre application prend en charge les **Comptes dans un annuaire organisationnel**, remplacez cette valeur par **Organizations**.
        3.    Si votre application prend en charge **Tous les utilisateurs de compte Microsoft**, remplacez cette valeur par **Common**.
    3.    Sur votre demande de jeton, définissez l’**étendue** sur « https://sts.mixedreality.azure.com//.default  ». Cette étendue indique à Azure AD que votre application demande un jeton pour le service d’émission de jeton de sécurité (STS) Mixed Reality.

Avec cela, votre application doit pouvoir obtenir un jeton Azure AD de la part de la bibliothèque MSAL. Vous pouvez définir ce jeton Azure AD en tant qu’**authenticationToken** sur votre objet de configuration de session cloud.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Authentification auprès du service Azure AD

L’option recommandée pour déployer des applications tirant parti d’Azure Spatial Anchors en production consiste à utiliser un service back-end qui assumera le rôle de service Broker pour les demandes d’authentification. Le modèle général doit être comme décrit dans ce diagramme :

![Une vue d’ensemble de l’authentification auprès d’Azure Spatial Anchors](./media/spatial-anchors-aad-authentication.png)

Ici, nous partons du principe que votre application utilise son propre mécanisme (par exemple Compte Microsoft, PlayFab, Facebook, Google ID, nom d’utilisateur/mot de passe personnalisés, et ainsi de suite) pour s’authentifier auprès de son service back-end. Une fois que vos utilisateurs sont authentifiés auprès de votre service back-end, celui-ci peut récupérer un jeton Azure AD, l’échanger contre un jeton d’accès pour Azure Spatial Anchors, et le renvoyer à votre application cliente.

Le jeton d’accès Azure AD est récupéré à l’aide de la [bibliothèque MSAL](../../active-directory/develop/msal-overview.md). Vous devez suivre les étapes indiquées dans le [guide de démarrage rapide Inscrire une application](../../active-directory/develop/quickstart-register-app.md), notamment :

1.    Configuration dans le portail Azure
    1.    Inscrivez votre application dans Azure AD :
        1.    Dans le portail Azure, accédez à **Azure Active Directory** et sélectionnez **Inscriptions d’applications**.
        2.    Sélectionnez **Nouvelle inscription d’application**.
        3.    Entrez le nom de votre application, sélectionnez **Application/API web** comme type d’application, puis entrez l’URL d’authentification pour votre service. Cliquez sur **Créer**.
        4.    Dans cette application, cliquez sur **Paramètres**, puis sélectionnez l’onglet **Certificats et secrets**. Créez une clé secrète client, sélectionnez une durée, puis cliquez sur **Ajouter**. Veillez à enregistrer la valeur du secret, car vous devrez l’inclure dans le code de votre service web.
    2.    Accordez à votre application et/ou aux utilisateurs l’accès à votre ressource :
        1.    Accédez à votre ressource Spatial Anchors dans le portail Azure.
        2.    Accédez à l’onglet **Contrôle d’accès (IAM)** .
        3.    Cliquez sur **Ajouter une attribution de rôle**.
        1.    [Sélectionnez un rôle](#role-based-access-control).
        2.    Dans le champ **Sélectionner**, entrez le nom des applications que vous avez créées et auxquelles vous souhaitez accorder l’accès. Si vous voulez que les utilisateurs de votre application aient différents rôles sur le compte Spatial Anchors, vous devez inscrire plusieurs applications dans Azure AD et attribuer à chacune un rôle distinct. Ensuite, implémentez votre logique d’autorisation afin d’utiliser le rôle approprié pour vos utilisateurs.
        3.    Remarque : Dans la sélection **Ajouter une attribution de rôle**, vous voudrez que le paramètre **Attribuer l’accès à** soit défini sur « Utilisateur, groupe ou principal du service Azure AD ».
    3.    Cliquez sur **Enregistrer**.
2.    Dans votre code (Remarque : Vous pouvez utiliser l’exemple de service fourni sur GitHub) :
    1.    Veillez à utiliser l’ID d’application, le secret d’application et l’URI de redirection de votre propre application Azure AD comme paramètres d’ID client, de secret et d’URI de redirection dans la bibliothèque MSAL
    2.    Définissez comme ID de locataire votre propre ID de locataire Azure ADD dans le paramètre d’autorité dans la bibliothèque MSAL.
    3.    Sur votre demande de jeton, définissez l’**étendue** sur « https://sts.mixedreality.azure.com//.default  »

Avec cela, votre service back-end peut récupérer un jeton Azure AD. Il peut ensuite l’échanger contre un jeton MR qu’il renverra au client. L’utilisation d’un jeton Azure AD pour récupérer un jeton MR s’effectue par le biais d’un appel REST. Voici un exemple d’appel :

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Où l’en-tête Authorization est mis en forme comme suit : `Bearer <Azure_AD_token>`

Et la réponse contient le jeton MR en texte brut.

Ce jeton MR est ensuite renvoyé au client. Votre application cliente peut ensuite le définir comme son jeton d’accès dans la configuration de session cloud.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour vous aider à contrôler le niveau d’accès accordé aux applications, services ou utilisateurs Azure AD de votre service, les rôles suivants ont été créés. Vous pouvez les affecter en fonction des besoins sur vos comptes Azure Spatial Anchors :

- **Propriétaire de compte Points d’ancrage Spatial** : les applications ou utilisateurs qui détiennent ce rôle peuvent créer des points d’ancrage spatial, les interroger et les supprimer. Quand vous vous authentifiez auprès de votre compte à l’aide de clés de compte, le rôle **Propriétaire de compte Points d’ancrage Spatial** est attribué au principal authentifié.
- **Contributeur de compte Points d’ancrage Spatial** : les applications ou utilisateurs qui détiennent ce rôle peuvent créer des points d’ancrage spatial, les interroger, mais pas les supprimer.
- **Lecteur de compte Points d’ancrage Spatial** : les applications ou utilisateurs qui détiennent ce rôle peuvent seulement interroger des points d’ancrage spatial. Ils ne peuvent pas en créer, ni en supprimer, ni mettre à jour des métadonnées sur des points d’ancrage spatial. Ce compte est utilisé généralement pour les applications où certains utilisateurs gèrent l’environnement tandis que d’autres peuvent uniquement rappeler les points d’ancrage précédemment placés dans cet environnement.

## <a name="next-steps"></a>Étapes suivantes

Créez votre première application avec Azure Spatial Anchors.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
