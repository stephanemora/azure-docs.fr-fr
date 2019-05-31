---
title: Utiliser Microsoft Authentication Library (MSAL) dans les clouds nationaux - plateforme d’identité Microsoft
description: Bibliothèque d’authentification Microsoft (MSAL) permet aux développeurs d’applications acquérir des jetons pour appeler des API web sécurisée. Ces API web peut être Microsoft Graph, autres APIs Microsoft, API web de partenaire ou votre propre API web. MSAL prend en charge plusieurs architectures et plateformes d’application.
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
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235264"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Utiliser MSAL dans un environnement de cloud national

[Clouds nationaux](authentication-national-cloud.md) sont physiquement isolées les instances d’Azure. Ces régions Azure contribuez à garantir que les exigences de conformité, de souveraineté et de résidence des données sont respectées dans les limites géographiques.

En plus de nuage Microsoft dans le monde entier, Microsoft Authentication Library (MSAL) permet les développeurs d’applications dans les clouds nationaux acquérir des jetons pour authentifier et appeler des API web sécurisée. Ces API web peut être Microsoft Graph ou autres APIs de Microsoft.

Y compris le nuage global, Azure Active Directory (Azure AD) est déployé dans les clouds nationaux suivants :  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Ce guide montre comment se connecter à travailler établissement scolaire, obtenir un jeton accès et appeler l’API Microsoft Graph dans le [cloud Azure Government](https://azure.microsoft.com/global-infrastructure/government/) environnement.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, assurez-vous que vous répondre à ces conditions préalables.

### <a name="choose-the-appropriate-identities"></a>Choisissez les identités appropriées

[Azure Government](https://docs.microsoft.com/azure/azure-government/) applications peuvent utiliser les identités Azure AD pour les administrations et publique d’Azure AD pour authentifier les utilisateurs. Étant donné que vous pouvez utiliser une de ces identités, vous devez déterminer quel point de terminaison autorité que vous devez choisir pour votre scénario :

- Azure AD Public : Généralement utilisée si votre organisation possède déjà un locataire Azure AD publique pour prendre en charge Office 365 (Public ou GCC) ou une autre application.
- AD Azure Government : Couramment utilisé si votre organisation dispose d’un locataire Azure AD pour les administrations au support technique Office 365 (GCC High ou DoD) déjà ou crée un nouveau locataire dans Azure Government AD.

Après avoir choisi, une attention particulière est là que vous effectuez votre inscription d’application. Si vous choisissez des identités Azure AD publique pour votre application Azure Government, vous devez inscrire l’application dans votre locataire Azure AD publique.

### <a name="get-an-azure-government-subscription"></a>Obtenir un abonnement Azure Government

Pour obtenir un abonnement Azure Government, consultez [gestion et la connexion à votre abonnement dans Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Si vous n’avez pas un abonnement Azure Government, créer un [compte gratuit](https://azure.microsoft.com/global-infrastructure/government/request/) avant de commencer.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

1. Connectez-vous au [Portail Azure](https://portal.azure.us/).
    
   Pour rechercher les points de terminaison de portail Azure pour les autres clouds nationaux, consultez [points de terminaison d’inscription](authentication-national-cloud.md#app-registration-endpoints).

1. Si votre compte donne votre accès à plusieurs locataires, sélectionnez votre compte dans le coin supérieur droit, définissez votre session de portail vers Azure AD souhaité client.
1. Accédez à la [inscriptions](https://aka.ms/ra/ff) page sur la plateforme d’identité Microsoft pour les développeurs.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **pris en charge les types de comptes**, sélectionnez **comptes dans n’importe quel répertoire organisation**.
1. Dans le **URI de redirection** section, sélectionnez le **Web** plate-forme et la valeur à l’URL de l’application basée sur votre serveur web. Consultez les sections suivantes pour obtenir des instructions sur la façon de configurer et obtenir l’URL de redirection dans Visual Studio et de nœud.
1. Sélectionnez **Inscription**.
1. Sur la page **Vue d’ensemble**, notez la valeur **ID d’application (client)** .
1. Ce didacticiel requiert que vous activiez le [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md). Dans le volet gauche de l’application inscrite, sélectionnez **authentification**.
1. Dans **paramètres avancés**, sous **octroi implicite**, sélectionnez le **les jetons d’ID** et **de jetons d’accès** cases à cocher. Jetons d’ID et jetons d’accès sont requis, car cette application a besoin pour connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Étape 2 :  Configurer votre serveur web ou un projet

- [Télécharger les fichiers projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pour un serveur web local, tel que le nœud.

  or

- [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Passez ensuite à [configurer votre application SPA JavaScript](#step-4-configure-your-javascript-spa) pour configurer l’exemple de code avant de l’exécuter.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Étape 3 : Connectez-vous à l’utilisateur à l’aide de la bibliothèque d’authentification Microsoft

Suivez les étapes de la [JavaScript didacticiel](tutorial-v2-javascript-spa.md#create-your-project) pour créer votre projet et l’intégrer avec MSAL pour connecter l’utilisateur.

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

- `Enter_the_Application_Id_here` est le **ID d’Application (client)** valeur pour l’application que vous avez inscrite.
- `Enter_the_Tenant_Info_Here` est définie sur une des options suivantes :
    - Si votre application prend en charge **comptes dans ce répertoire d’organisation**, de remplacer cette valeur avec l’ID de locataire ou nom (par exemple, contoso.microsoft.com) du locataire.
    - Si votre application prend en charge **comptes dans n’importe quel répertoire organisation**, remplacez cette valeur avec `organizations`.
    
    Pour rechercher des points de terminaison d’authentification pour tous les clouds nationaux, consultez [points de terminaison de l’authentification Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Les comptes Microsoft personnels ne sont pas pris en charge dans les clouds nationaux.
  
- `graphEndpoint` est le point de terminaison Microsoft Graph pour le cloud de Microsoft pour le gouvernement des États-Unis.

   Pour rechercher des points de terminaison Microsoft Graph pour tous les clouds nationaux, consultez [points de terminaison Microsoft Graph dans les clouds nationaux](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Vous pouvez utiliser MSAL.NET pour connecter des utilisateurs, acquérir des jetons et appeler l’API Microsoft Graph dans les clouds nationaux.

Les didacticiels suivants montrent comment créer une application Web .NET Core 2.2 MVC. L’application utilise OpenID Connect pour connecter les utilisateurs avec un compte professionnel et scolaire dans une organisation qui appartient à un cloud national.

- Pour connecter des utilisateurs et acquérir des jetons, suivez [ce didacticiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Pour appeler l’API Microsoft Graph, suivez [ce didacticiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Allemagne](https://docs.microsoft.com/azure/germany/)