---
title: Utiliser Microsoft Authentication Library (MSAL) dans les Clouds nationaux - plateforme d’identité Microsoft
description: Bibliothèque d’authentification Microsoft (MSAL) permet aux développeurs d’applications acquérir des jetons pour appeler des API web sécurisée. Ces API web peut être Microsoft Graph, autres APIS Microsoft, API web de tiers ou vos propres API web. MSAL prend en charge plusieurs plateformes et architectures d’application.
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
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075789"
---
# <a name="use-msal-in-national-cloud-environment"></a>Utiliser MSAL dans un environnement de cloud National

[Clouds nationaux](authentication-national-cloud.md) sont physiquement isolées les instances d’Azure. Ces régions d’Azure sont conçues pour garantir que les conditions de résidence, de souveraineté et de conformité des données sont respectées au sein de limites géographiques.

En plus de Microsoft cloud dans le monde entier, Microsoft Authentication Library (MSAL) permet également les développeurs d’applications dans les clouds nationaux acquérir des jetons pour authentifier et appeler des API web sécurisée. Ces API web peut être Microsoft Graph ou autres APIS de Microsoft.

Notamment le nuage global, Azure Active Directory (Azure AD) est déployé dans les clouds nationaux suivants :  

- Azure US Government
- Azure China 21Vianet
- Azure Germany

Ce guide explique comment connecter des comptes professionnels et scolaires, obtenir un jeton accès et appeler l’API Microsoft Graph dans [Microsoft cloud US Government](https://azure.microsoft.com/global-infrastructure/government/) environnement.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, assurez-vous que vous remplissez ces conditions préalables.

### <a name="choose-the-appropriate-identities"></a>Choisissez les identités appropriées

[Azure Government](https://docs.microsoft.com/azure/azure-government/) applications peuvent utiliser les identités Azure AD pour les administrations, ainsi que des identités publiques d’Azure AD pour authentifier les utilisateurs. Étant donné que vous pouvez utiliser une de ces identités, vous devez comprendre et à décider quel point de terminaison autorité que vous devez choisir pour votre scénario :

- Azure AD Public : Généralement utilisée si votre organisation possède déjà un locataire Azure AD publique pour prendre en charge Office 365 (Public ou GCC) ou une autre application.
- AD Azure Government : Couramment utilisé si votre organisation dispose d’un locataire Azure AD pour les administrations au support technique Office 365 (GCC High ou DoD) déjà ou créez un nouveau locataire dans Azure Government AD.

Une fois choisi, une attention particulière est l’endroit où vous effectuez votre inscription d’application. Si vous choisissez des identités Azure AD publique pour votre application Azure Government, vous devez inscrire l’application dans votre locataire Azure AD publique.

### <a name="get-an-azure-government-subscription"></a>Obtenir un abonnement Azure Government

- Pour obtenir un abonnement Azure Government, consultez [la gestion et la connexion à votre abonnement dans Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Si vous n’avez pas un abonnement Azure Government, créer un [compte gratuit](https://azure.microsoft.com/global-infrastructure/government/request/) avant de commencer.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

1. Connectez-vous au [portail Azure](https://portal.azure.us/) pour inscrire une application.
    1. Pour rechercher les points de terminaison de portail Azure pour les autres clouds nationaux, consultez [points de terminaison d’inscription](authentication-national-cloud.md#app-registration-endpoints)

1. Si votre compte donne votre accès à plusieurs locataires, sélectionnez votre compte dans l’angle supérieur droit, définissez votre session de portail vers Azure AD souhaité client.
1. Accédez à la page [Inscriptions des applications](https://aka.ms/ra/ff) de la plateforme d’identité Microsoft pour les développeurs.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **pris en charge les types de comptes**, sélectionnez **comptes dans n’importe quel répertoire organisation**.
1. Sous la section **URI de redirection**, sélectionnez la plateforme **Web** et définissez la valeur sur l’URL de l’application basée sur votre serveur web. Consultez les sections ci-dessous pour obtenir des instructions permettant de définir et d’obtenir l’URL de redirection dans Visual Studio et Node.
1. Lorsque vous avez terminé, sélectionnez **Inscrire**.
1. Sur la page **Vue d’ensemble**, notez la valeur **ID d’application (client)**.
1. Ce didacticiel requiert le [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md) doit être activé. Dans le volet de navigation de gauche de l’application inscrite, sélectionnez **Authentification**.
1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et jetons d’accès sont nécessaires dans la mesure où cette application doit connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Étape 2 :  Configurer votre serveur web ou un projet

- [Télécharger les fichiers projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pour un serveur web local, tel que le nœud.

  or

- [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Puis passez à la [« Configurer votre application SPA JavaScript »](#step-4-configure-your-javascript-spa) pour configurer l’exemple de code avant son exécution.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Étape 3 : Utiliser Microsoft Authentication Library (MSAL) pour connecter l’utilisateur

Suivez les étapes de [Javascript didacticiel](tutorial-v2-javascript-spa.md#create-your-project) pour créer votre projet et d’intégrer avec Microsoft Authentication Library (MSAL) pour connecter l’utilisateur.

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

Où :

- `Enter_the_Application_Id_here` : est l’**ID d’application (client)** pour l’application que vous avez inscrite.
- `Enter_the_Tenant_Info_Here` : est défini sur l’une des options suivantes :
    - Si votre application prend en charge **comptes dans ce répertoire d’organisation**, remplacez cette valeur avec le **ID de locataire** ou **nom_client** (par exemple, Contoso.Microsoft.com)
    - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`
    -  Pour rechercher des points de terminaison d’authentification pour tous les clouds nationaux, consultez [points de terminaison de l’authentification Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Scénarios de comptes (MSA) personnels Microsoft ne sont pas pris en charge dans les clouds nationaux.
  
-   `graphEndpoint` -le point de terminaison Microsoft Graph de Microsoft cloud pour gouvernement des États-Unis.
    -  Pour rechercher des points de terminaison Microsoft Graph pour tous les clouds nationaux, consultez [points de terminaison Microsoft Graph dans un cloud National](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

MSAL .NET vous permet de connecter des utilisateurs, acquérir le jeton et appeler des API Microsoft Graph dans les Clouds nationaux.

Le didacticiel suivant montre comment créer une application Web .NET Core 2.2 MVC qui utilise OpenID Connect pour connecter les utilisateurs avec leurs comptes « professionnels et scolaires » dans leur organisation appartenant à des clouds nationaux.

- Pour connecter des utilisateurs et acquérir le jeton, suivez [ce didacticiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Pour appeler l’API Microsoft Graph, suivez [ce didacticiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Allemagne](https://docs.microsoft.com/azure/germany/)