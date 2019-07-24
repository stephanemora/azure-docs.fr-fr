---
title: Utiliser Microsoft Authentication Library (MSAL) dans les clouds nationaux - Plateforme d’identité Microsoft
description: Microsoft Authentication Library (MSAL) permet aux développeurs d’applications d’acquérir des jetons afin d’appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph, d’autres API Microsoft, des API web de partenaires ou vos propres API web. MSAL prend en charge plusieurs architectures et plateformes d’application.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235264"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Utiliser MSAL dans un environnement de cloud national

[Les clouds nationaux](authentication-national-cloud.md) sont des instances d’Azure physiquement isolées. Ces régions d’Azure aident à garantir que les conditions de résidence, de souveraineté et de conformité des données sont respectées au sein de limites géographiques.

En plus du cloud Microsoft global, Microsoft Authentication Library (MSAL) permet aux développeurs d’applications dans des clouds nationaux d’acquérir des jetons pour authentifier et appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph ou d’autres API Microsoft.

Outre le cloud global, Azure Active Directory (Azure AD) est déployé dans les clouds nationaux suivants :  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Ce guide montre comment se connecter à des comptes professionnels et scolaires, obtenir un jeton accès et appeler l’API Microsoft Graph dans l’environnement [cloud Azure Government](https://azure.microsoft.com/global-infrastructure/government/).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous répondez aux exigences suivantes.

### <a name="choose-the-appropriate-identities"></a>Choisir les identités appropriées

Les applications [Azure Government](https://docs.microsoft.com/azure/azure-government/) peuvent utiliser des identités Azure AD Government et des identités Azure AD Public pour authentifier des utilisateurs. Étant donné que vous pouvez utiliser l’une de ces identités, vous devez déterminer quel point de terminaison d’autorité à choisir pour votre scénario :

- Azure AD Public : généralement utilisé si votre organisation possède déjà un locataire Azure AD Public pour prendre en charge Office 365 (Public ou GCC) ou une autre application.
- Azure AD Government : généralement utilisé si votre organisation dispose d’un locataire Azure AD Government pour prendre en charge Office 365 (GCC High ou DoD) ou si elle crée un nouveau locataire dans Azure Government AD.

Après avoir choisi, une attention particulière est portée là où vous effectuez votre inscription d’application. Si vous choisissez des identités Azure AD Public pour votre application Azure Government, vous devez inscrire l’application dans votre locataire Azure AD Public.

### <a name="get-an-azure-government-subscription"></a>Obtenir un abonnement Azure Government

Pour obtenir un abonnement Azure Government, consultez [Gestion et connexion à votre abonnement dans Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/global-infrastructure/government/request/) avant de commencer.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

1. Connectez-vous au [Portail Azure](https://portal.azure.us/).
    
   Pour rechercher des points de terminaison de portail Azure pour d’autres clouds nationaux, consultez [Points de terminaison d’inscription d’application](authentication-national-cloud.md#app-registration-endpoints).

1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions d’applications](https://aka.ms/ra/ff) de la plateforme d’identité Microsoft pour les développeurs.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel**.
1. Sous la section **URI de redirection**, sélectionnez la plateforme **Web** et définissez la valeur sur l’URL de l’application basée sur votre serveur web. Consultez les prochaines sections pour obtenir des instructions permettant de définir et d’obtenir l’URL de redirection dans Visual Studio et Node.
1. Sélectionnez **Inscription**.
1. Sur la page **Vue d’ensemble**, notez la valeur **ID d’application (client)** .
1. Ce didacticiel vous demande d’activer le [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md). Dans le volet gauche de l’application inscrite, sélectionnez **Authentification**.
1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et les jetons d’accès sont nécessaires, car cette application doit connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Étape 2 :  Configurer le serveur web ou projet

- [Télécharger les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pour un serveur web local, tel que Node.

  or

- [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Passez ensuite à [Configurer votre application SPA JavaScript](#step-4-configure-your-javascript-spa) pour configurer l’exemple de code avant de l’exécuter.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Étape 3 : Utiliser Microsoft Authentication Library pour connecter l’utilisateur

Suivez les étapes du [didacticiel JavaScript](tutorial-v2-javascript-spa.md#create-your-project) pour créer votre projet et l’intégrer avec MSAL pour connecter l’utilisateur.

### <a name="step-4-configure-your-javascript-spa"></a>Étape 4 : Configurer une application SPA JavaScript

Dans le fichier `index.html` créé au cours de la configuration du projet, ajoutez les informations d’inscription d’application. Ajoutez le code ci-après dans la partie supérieure entre les balises `<script></script>` dans le corps de votre fichier `index.html` :

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Dans ce code :

- `Enter_the_Application_Id_here` est la valeur **ID d’application (client)** pour l’application que vous avez inscrite.
- `Enter_the_Tenant_Info_Here` est défini sur l’une des options suivantes :
    - Si votre application prend en charge les **Comptes dans cet annuaire organisationnel**, remplacez cette valeur par l’ID de locataire ou le nom du locataire (par exemple, contoso.microsoft.com).
    - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`.
    
    Pour rechercher des points de terminaison d’authentification pour tous les clouds nationaux, consultez [Points de terminaison d’authentification Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Les comptes Microsoft personnels ne sont pas pris en charge dans les clouds nationaux.
  
- `graphEndpoint` est le point de terminaison Microsoft Graph pour Microsoft Cloud for US Government.

   Pour rechercher des points de terminaison Microsoft Graph pour tous les clouds nationaux, consultez [Points de terminaison Microsoft Graph dans des clouds nationaux](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Vous pouvez utiliser MSAL.NET pour connecter des utilisateurs, acquérir des jetons et appeler l’API Microsoft Graph dans des clouds nationaux.

Les didacticiels suivants montrent comment créer une application Web MVC .NET Core 2.2. L’application utilise OpenID Connect pour connecter des utilisateurs avec un compte professionnel et scolaire dans une organisation appartenant à un cloud national.

- Pour connecter des utilisateurs et acquérir des jetons, suivez [ce didacticiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Pour appeler l’API Microsoft Graph, suivez [ce didacticiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Allemagne](https://docs.microsoft.com/azure/germany/)