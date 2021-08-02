---
title: Configurer l’authentification dans un exemple d’application web à l’aide d’Azure Active Directory B2C
description: Utilisation d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application web ASP.NET.
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
ms.openlocfilehash: 23b66bef9de9fc83884f882eee3ad21aba695b48
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071324"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application web à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application web ASP.NET pour illustrer comment ajouter une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications web.

> [!IMPORTANT]
> L’exemple d’application web ASP.NET référencé dans cet article ne peut pas être utilisé pour appeler une API REST, car il renvoie un jeton d’ID, mais pas de jeton d’accès. Pour une application web capable d’appeler une API REST, consultez [Sécuriser une API web générée avec ASP.NET Core à l’aide d’Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).  

## <a name="overview"></a>Vue d’ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0 que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application web utilise [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web). Microsoft Identity Web est un ensemble de bibliothèques ASP.NET Core qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications web. 

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur accède à l’application web et sélectionne **Connexion**. 
1. L’application initie une demande d’authentification et redirige l’utilisateur vers Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md), [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte à l’aide d’un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un jeton d’ID à l’application.
1. L’application valide le jeton d’ID, lit les revendications et renvoie une page sécurisée à l’utilisateur.

Lorsque le jeton d’ID a expiré ou que la session de l’application est invalidée, l’application initie une nouvelle demande d’authentification et redirige l’utilisateur vers Azure AD B2C. Si la [session d’authentification unique](session-behavior.md) d’Azure AD B2C est active, Azure AD B2C émet un jeton d’accès sans inviter l’utilisateur à se reconnecter. Si la session Azure AD B2C expire ou devient non valide, l’utilisateur est invité à se connecter à nouveau.

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

## <a name="step-2-register-a-web-application"></a>Étape 2 : Inscrire une application web

Pour permettre à votre application de se connecter avec Azure AD B2C, inscrivez votre application dans le répertoire Azure AD B2C. L’inscription de votre application établit une relation de confiance entre l’application et Azure AD B2C.  

Pendant l’inscription de l’application, vous spécifiez l’**URI de redirection**. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé par Azure AD B2C après s’être authentifié avec Azure AD B2C. Le processus d’inscription de l’application génère un **ID d’application**, également appelé **ID client**, qui identifie votre application de façon unique. Une fois votre application inscrite, Azure AD B2C utilise à la fois l’ID d’application et l’URI de redirection pour créer des demandes d’authentification. 

### <a name="register-the-app"></a>Enregistrer l'application

Effectuez les étapes suivantes pour créer l’inscription d’application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *webapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://localhost:5001/signin-oidc` dans la zone de texte de l’URL.
1. Sous **Autorisations**, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline access**.
1. Sélectionnez **Inscription**.
1. Sélectionnez **Vue d’ensemble**.
1. Enregistrez **l’ID d’application (client)** que vous utiliserez ultérieurement pour configurer l'application web.

    ![Obtenir votre ID d’application](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="enable-id-tokens"></a>Activer les jetons d’ID

Pour les applications web qui demandent un jeton d’ID directement à Azure AD B2C, activez le flux d’octroi implicite dans l’inscription de l’application.

1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Authentification**.
1. Sous **Octroi implicite**, cochez la case **Jetons d’ID**.
1. Sélectionnez **Enregistrer**.

## <a name="step-3-get-the-web-app-sample"></a>Étape 3 : Obtenir l’exemple d’application web

[Téléchargez un fichier zip](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip) ou clonez l’exemple d’application web à partir de GitHub. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extrayez l’exemple de fichier dans un dossier où la longueur totale du chemin d’accès est inférieure à 260 caractères.

## <a name="step-4-configure-the-sample-application"></a>Étape 4 : Configurer l’exemple d’application

Dans le dossier d’exemple, sous le dossier `1-WebApp-OIDC/1-5-B2C/`, ouvrez le projet **WebApp-OpenIDConnect-DotNet.csproj** avec Visual Studio ou Visual Studio Code. 

Dans le dossier racine du projet, ouvrez le fichier `appsettings.json`. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. Mettez à jour les propriétés suivantes des paramètres de l’application : 

|Section  |Clé  |Valeur  |
|---------|---------|---------|
|AzureAdB2C|Instance| La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `https://contoso.b2clogin.com`.|
|AzureAdB2C|Domain| Le [nom de locataire](tenant-management.md#get-your-tenant-name) complet de votre locataire Azure AD B2C. Par exemple : `contoso.onmicrosoft.com`.|
|AzureAdB2C|ClientId| L’ID d’application de l’API web de l’[étape 2](#step-2-register-a-web-application).|
|AzureAdB2C|SignUpSignInPolicyId|Le flux d’utilisateurs ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|

Votre fichier config final doit ressembler au JSON suivant :

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-application"></a>Étape 5 : Exécuter l’exemple d’application

1. Générez et exécutez le projet.
1. Accédez à https://localhost:5001. 
1. Sélectionnez **Inscription/Connexion**.

    ![Sélectionner la connexion ou l’inscription](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. Terminez le processus d’inscription ou de connexion.

Une fois l’authentification réussie, votre nom d’affichage s’affiche dans la barre de navigation. Pour afficher les revendications que le jeton d’Azure AD B2C renvoie à votre application, sélectionnez **Revendications**.

![Revendications du jeton d’application web](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>Déployer votre application 

Dans une application de production, l’URI de redirection de l’inscription de l’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/signin-oidc`. 

Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier. Les restrictions suivantes s’appliquent aux URI de redirection :

* L’URL de réponse doit commencer par le schéma `https`.
* L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[exemple de code](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)
* Découvrir comment [activer l’authentification dans votre propre application web à l’aide d’Azure AD B2C](enable-authentication-web-application.md)