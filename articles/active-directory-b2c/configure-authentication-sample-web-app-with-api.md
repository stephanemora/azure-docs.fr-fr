---
title: Configurer l’authentification dans un exemple d’application web qui appelle une API web à l’aide d’Azure Active Directory B2C
description: Utilisation d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application web ASP.NET qui appelle une API web.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: fab89113665b8b9534118fc443474349f4e9c3b6
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072993"
---
# <a name="configure-authentication-in-a-sample-web-application-that-calls-a-web-api-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application web qui appelle une API web à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application web ASP.NET qui appelle une API web pour illustrer comment ajouter une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications web.

> [!IMPORTANT]
> L’exemple d’application web ASP.NET référencé dans cet article est utilisé pour appeler une API web avec un jeton du porteur. Pour une application web qui n’appelle pas d’API web, consultez [Configurer l’authentification dans un exemple d’application web à l’aide d’Azure Active Directory B2C](configure-authentication-sample-web-app.md).  

## <a name="overview"></a>Vue d’ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0 que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application web utilise [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web). Microsoft Identity Web est un ensemble de bibliothèques ASP.NET Core qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications web qui peuvent appeler une API web sécurisée. 

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur accède à l’application web et sélectionne **Connexion**.
1. L’application initie la demande d’authentification et redirige l’utilisateur vers Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md), [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte avec un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un code d’autorisation à l’application.
1. L’application :
    1. échange le code d’autorisation contre un jeton d’ID, un jeton d’accès et un jeton d’actualisation ;
    1. lit les revendications du jeton d’ID et conserve un cookie d’autorisation d’application ;
    1. stocke le jeton d’actualisation dans un cache en mémoire pour une utilisation ultérieure.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, vous devez inscrire deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application web** permet à votre application de se connecter grâce à Azure AD B2C. Pendant l’inscription de l’application, vous spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé par Azure AD B2C une fois son authentification avec Azure AD B2C terminée. Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application de façon unique. Vous créez également une *clé secrète client*, qui est utilisée par votre application pour acquérir les jetons en toute sécurité.

- L’inscription de l’**API web** permet à votre application d’appeler une API web sécurisée. L’inscription comprend les *étendues* de l’API web. Les étendues permettent de gérer les autorisations d’accès aux ressources protégées, telles que votre API web. Vous accordez les autorisations de l’application web aux étendues de l’API web. Lorsqu’un jeton d’accès est demandé, votre application spécifie les autorisations souhaitées dans le paramètre d’étendue de la requête.  

Les diagrammes suivants décrivent l’inscription des applications et l’architecture de l’application.

![Application web avec inscriptions et jetons d’appel d’API web](./media/configure-authentication-sample-web-app-with-api/web-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Se déconnecter

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur exécutant l’un des éléments suivants : 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 version 16.8 ou ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement web et ASP.NET**
* [Kit SDK .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pour Visual Studio Code (dernière version)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit SDK .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-web-applications"></a>Étape 2 : Inscrire des applications web

Au cours de cette étape, vous allez créer l’application web et l’inscription de l’application API web, puis spécifier les étendues de votre API web.

### <a name="21-register-the-web-api-app"></a>2.1 Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Configurer les étendues de l’application API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-web-app"></a>2.3 Inscrire l’application web

Effectuez les étapes suivantes pour créer l’inscription d’application web :

1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *webapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://localhost:5000/signin-oidc` dans la zone de texte de l’URL.
1. Sous **Autorisations**, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline access**.
1. Sélectionnez **Inscription**.
1. Une fois l’inscription de l’application terminée, sélectionnez **Vue d’ensemble**.
1. Enregistrez **l’ID d’application (client)** que vous utiliserez ultérieurement pour configurer l'application web.

    ![Obtenir l’ID de votre application web](./media/configure-authentication-sample-web-app-with-api/get-azure-ad-b2c-app-id.png)  

### <a name="24-create-a-web-app-client-secret"></a>2.4 Créer une clé secrète client d’application web

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]


### <a name="25-grant-the-web-app-permissions-for-the-web-api"></a>2.5 Accorder à l’application web les autorisations pour l’API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-web-app-sample"></a>Étape 3 : Obtenir l’exemple d’application web

[Téléchargez un fichier zip](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip) ou clonez l’exemple d’application web à partir de GitHub. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extrayez l’exemple de fichier dans un dossier où la longueur totale du chemin d’accès est inférieure à 260 caractères.

## <a name="step-4-configure-the-sample-web-api"></a>Étape 4 : Configurer l’exemple d’API web

Dans le dossier d’exemple, sous le dossier `4-WebApp-your-API/4-2-B2C/TodoListService`, ouvrez le projet **TodoListService.csproj** avec Visual Studio ou Visual Studio Code. 

Dans le dossier racine du projet, ouvrez le fichier `appsettings.json`. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application API web utilise ces informations pour valider le jeton d’accès que l’application web transmet comme jeton du porteur. Mettez à jour les propriétés suivantes des paramètres de l’application :

|Section  |Clé  |Valeur  |
|---------|---------|---------|
|AzureAdB2C|Instance| La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `https://contoso.b2clogin.com`.|
|AzureAdB2C|Domain| Le [nom de locataire](tenant-management.md#get-your-tenant-name) complet de votre locataire Azure AD B2C. Par exemple : `contoso.onmicrosoft.com`.|
|AzureAdB2C|ClientId| L’ID d’application de l’API web de l’[étape 2.1](#21-register-the-web-api-app).|
|AzureAdB2C|SignUpSignInPolicyId|Le flux d’utilisateurs ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|

Votre fichier config final doit ressembler au JSON suivant :

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More setting here
}
```

### <a name="41-set-the-permission-policy"></a>4.1 Définir la stratégie d’autorisation

L’API web vérifie que l’utilisateur s’est authentifié avec le jeton du porteur et que le jeton du porteur possède les étendues acceptées configurées. Si le jeton du porteur n’a pas l’une de ces étendues acceptées, l’API web renvoie le code d’état HTTP 403 (Interdit) et écrit dans le corps de la réponse un message indiquant les étendues attendues dans le jeton. 

Pour configurer les étendues acceptées, ouvrez la classe `Controller/TodoListController.cs` et définissez le nom de l’étendue, sans l’URI complet.

```csharp
[RequiredScope("tasks.read")]
```

### <a name="42-run-the-sample-web-api-app"></a>4.2 Exécuter l’exemple d’application API web

Pour permettre à l’application web d’appeler l’exemple d’API web, suivez ces étapes pour exécuter l’API web :

1. Le cas échéant, restaurez les dépendances.
1. Générez et exécutez le projet.
1. Une fois le projet généré, Visual Studio ou Visual Studio Code lance l’API web dans les navigateurs avec l’adresse suivante : https://localhost:44332.

## <a name="step-5-configure-the-sample-web-app"></a>Étape 5 : Configurer l’exemple d’application web

Dans le dossier d’exemple, sous le dossier `4-WebApp-your-API/4-2-B2C/Client`, ouvrez le projet **TodoListClient.csproj** avec Visual Studio ou Visual Studio Code. 

Dans le dossier racine du projet, ouvrez le fichier `appsettings.json`. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application web utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter l’utilisateur et acquérir des jetons et les valider. Mettez à jour les propriétés suivantes des paramètres de l’application :

|Section  |Clé  |Valeur  |
|---------|---------|---------|
|AzureAdB2C|Instance| La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `https://contoso.b2clogin.com`.|
|AzureAdB2C|Domain| Le [nom de locataire](tenant-management.md#get-your-tenant-name) complet de votre locataire Azure AD B2C. Par exemple : `contoso.onmicrosoft.com`.|
|AzureAdB2C|ClientId| L’ID d’application web de l’[étape 2.1](#21-register-the-web-api-app).|
|AzureAdB2C | ClientSecret | Le secret de l’application web de l’[étape 2.4](#24-create-a-web-app-client-secret). | 
|AzureAdB2C|SignUpSignInPolicyId|Le flux d’utilisateurs ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
| TodoList | TodoListScope | Les étendues que vous avez créées à l’[étape 2.5](#25-grant-the-web-app-permissions-for-the-web-api).|
| TodoList | TodoListBaseAddress | L’URI de base de votre API web, par exemple `https://localhost:44332`.|

Votre fichier config final doit ressembler au JSON suivant :

```JSon
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-app-application-id>",
    "ClientSecret": "<web-app-application-secret>",  
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  "TodoList": {
    "TodoListScope": "https://contoso.onmicrosoft.com/api/demo.read",
    "TodoListBaseAddress": "https://localhost:44332"
  }
}
```


## <a name="step-6-run-the-sample-web-app"></a>Étape 6 : Exécuter l’exemple d’application web

1. Générez et exécutez le projet.
1. Accédez à https://localhost:5000. 
1. Terminez le processus d’inscription ou de connexion.

Une fois l’authentification réussie, votre nom d’affichage s’affiche dans la barre de navigation. Pour afficher les revendications que le jeton d’Azure AD B2C renvoie à votre application, sélectionnez **TodoList**.

![Revendications du jeton d’application web](./media/configure-authentication-sample-web-app-with-api/web-api-to-do-list.png)


## <a name="deploy-your-application"></a>Déployer votre application

Dans une application de production, l’URI de redirection de l’inscription de l’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/signin-oidc`. 

Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier. Les restrictions suivantes s’appliquent aux URI de redirection :

* L’URL de réponse doit commencer par le schéma `https`.
* L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. 

### <a name="token-cache-for-a-web-app"></a>Cache de jeton d’une application web

L’exemple d’application web utilise la sérialisation du cache de jeton en mémoire. Cette implémentation est idéale pour les tests. Elle est également adaptée aux applications de production, mais sachez que le cache de jeton est perdu lorsque l’application web est redémarrée. 

Pour un environnement de production, nous vous recommandons d’utiliser un cache en mémoire distribuée. Par exemple, le cache Redis, NCache ou un cache SQL Server. Pour plus d’informations sur les implémentations du cache en mémoire distribuée, consultez [Cache de jeton d’une application web](../active-directory/develop/msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[exemple de code](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)
* Découvrir comment [activer l’authentification dans votre propre application web à l’aide d’Azure AD B2C](enable-authentication-web-application.md)
