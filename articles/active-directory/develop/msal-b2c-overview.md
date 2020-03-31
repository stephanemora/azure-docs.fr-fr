---
title: Utiliser MSAL avec Azure Active Directory B2C | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) permet aux applications d’interagir avec Azure AD B2C et d’acquérir des jetons pour appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph, d’autres API Microsoft, des API web de tiers ou vos propres API web.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696450"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Utiliser Microsoft Authentication Library pour interagir avec Azure Active Directory B2C

Microsoft Authentication Library (MSAL) permet aux développeurs d’applications d’authentifier les utilisateurs avec des identités sociales et locales à l’aide d’[Azure Active Directory B2C (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C est un service de gestion des identités. Il vous permet de personnaliser et de contrôler la façon dont les clients s’inscrivent, se connectent et gèrent leurs profils lorsqu’ils utilisent vos applications.

Azure AD B2C vous permet également de personnaliser l’interface utilisateur de vos applications pour fournir une expérience fluide à vos clients.

Ce didacticiel montre comment utiliser MSAL pour interagir avec Azure AD B2C.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), créez-en un. Vous pouvez également utiliser un locataire Azure AD B2C existant.

## <a name="javascript"></a>JavaScript

Les étapes suivantes montrent comment une application monopage peut utiliser Azure AD B2C pour s’inscrire, se connecter et appeler une API web protégée.

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

Pour implémenter l’authentification, vous devez d’abord inscrire votre application. Pour des instructions détaillées, voir [Inscrire votre application](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c).

### <a name="step-2-download-the-sample-application"></a>Étape 2 : Téléchargement de l'exemple d'application

Téléchargez l’exemple en tant que fichier zip ou clonez-le à partir de GitHub :

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Étape 3 : configurer l’authentification ;

1. Ouvrez le fichier **index.html** dans l’exemple.

1. Configurez l’exemple avec l’ID de client et la clé que vous avez enregistrés lors de l’inscription de votre application. Modifiez les lignes de code suivantes en remplaçant les valeurs par les noms de votre répertoire et de vos API :

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Le nom de [flux d’utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) utilisé dans ce tutoriel est **B2C_1_signupsignin1**. Si vous utilisez un autre nom de flux d’utilisateur, définissez la valeur **autorité** sur ce nom.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Étape 4 : configurer votre application pour utiliser `b2clogin.com`

Vous pouvez utiliser `b2clogin.com` à la place de `login.microsoftonline.com` comme URL de redirection. Vous pouvez le faire dans votre application Azure AD B2C lorsque vous configurez un fournisseur d’identité pour l’inscription et la connexion.

L’utilisation de `b2clogin.com` dans le contexte de `https://your-tenant-name.b2clogin.com/your-tenant-guid` produit les effets suivants :

- Les services Microsoft utilisent moins d’espace dans l’en-tête du cookie.
- Vos URL n’incluent plus de référence à Microsoft. Par exemple, votre application Azure AD B2C fait probablement référence à `login.microsoftonline.com`.

 Pour utiliser `b2clogin.com`, vous devez mettre à jour la configuration de votre application.  

- Affectez à la propriété **validateAuthority** la valeur `false`, afin que les redirections à l’aide de `b2clogin.com` puissent se produire.

L’exemple suivant montre une façon de définir la propriété :

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Votre application Azure AD B2C fait probablement référence à `login.microsoftonline.com` à plusieurs emplacements, par exemple au niveau des références de flux d’utilisateur ou des points de terminaison de jeton. Assurez-vous que votre point de terminaison d’autorisation, votre point de terminaison de jeton et votre émetteur ont été mis à jour pour utiliser `your-tenant-name.b2clogin.com`.

Suivez [cet exemple de MSAL JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) pour savoir comment utiliser la préversion de MSAL pour JavaScript (MSAL.js). L’exemple obtient un jeton d’accès et appelle une API sécurisée par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personnalisation de l’interface utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)