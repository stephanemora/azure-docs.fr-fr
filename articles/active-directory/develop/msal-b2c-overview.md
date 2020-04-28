---
title: Utiliser MSAL avec Azure Active Directory B2C | Azure
titleSuffix: Microsoft identity platform
description: La bibliothèque d'authentification Microsoft pour JavaScript (MSAL.js) permet aux applications de travailler avec Azure AD B2C et d'acquérir des jetons pour appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph, d’autres API Microsoft, des API web de tiers ou vos propres API web.
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
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534480"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Utiliser la bibliothèque d’authentification Microsoft pour JavaScript pour travailler avec Azure Active Directory B2C

La [bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) permet aux développeurs JavaScript d’authentifier les utilisateurs avec des identités sociales et locales à l’aide d’[Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). En utilisant Azure AD B2C en tant que service de gestion des identités, vous pouvez personnaliser et contrôler la façon dont les clients s’inscrivent, se connectent et gèrent leurs profils lorsqu’ils utilisent vos applications.

Azure AD B2C vous permet également de personnaliser l’interface utilisateur de vos applications au cours du processus d’authentification pour fournir une expérience fluide à vos clients.

Cet article montre comment utiliser MSAL.js pour travailler avec Azure AD B2C et récapitule les points clés dont vous devez être conscient. Pour une présentation complète et un tutoriel, consultez la [documentation sur Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), commencez par en créer un maintenant (vous pouvez également utiliser un locataire Azure AD B2C existant si vous en avez déjà un).

Cette démonstration contient deux parties :

- Comment protéger une API web.
- Comment inscrire une application à page unique pour authentifier et appeler *cette* API web.

## <a name="nodejs-web-api"></a>API web Node.js

> [!NOTE]
> À ce stade, MSAL.js pour Node est toujours en développement (consultez la [feuille de route](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). En attendant, nous vous suggérons d’utiliser [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad), une bibliothèque d’authentification pour Node.js développée et prise en charge par Microsoft.

Les étapes suivantes montrent comment une **API web** peut utiliser Azure AD B2C pour se protéger et exposer des étendues sélectionnées à une application cliente.

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

Pour protéger votre API web avec Azure AD B2C, vous devez commencer par l’inscrire. Pour des instructions détaillées, voir [Inscrire votre application](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications).

### <a name="step-2-download-the-sample-application"></a>Étape 2 : Téléchargement de l'exemple d'application

Téléchargez l’exemple en tant que fichier zip ou clonez-le à partir de GitHub :

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Étape 3 : configurer l’authentification ;

1. Ouvrez le fichier `config.js` dans l’exemple.

2. Configurez l’exemple avec les informations d’identification de l’application que vous avez obtenues précédemment lors de l’inscription de votre application. Modifiez les lignes de code suivantes en remplaçant les valeurs par les noms de vos paramètres clientID, host, tenantId et policyName.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Pour plus d’informations, consultez cet [exemple d’API web Node.js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Les étapes suivantes montrent comment une **application monopage** peut utiliser Azure AD B2C pour s’inscrire, se connecter et appeler une API web protégée.

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

Pour implémenter l’authentification, vous devez d’abord inscrire votre application. Pour des instructions détaillées, voir [Inscrire votre application](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications).

### <a name="step-2-download-the-sample-application"></a>Étape 2 : Téléchargement de l'exemple d'application

Téléchargez l’exemple en tant que fichier zip ou clonez-le à partir de GitHub :

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Étape 3 : configurer l’authentification ;

La configuration de votre application présente deux points intéressants :

- Configurer le point de terminaison d’API et les étendues exposées
- Configurer les paramètres d’authentification et les étendues de jetons

1. Ouvrez le fichier `apiConfig.js` dans l’exemple.

2. Configurez l’exemple avec les paramètres que vous avez obtenus précédemment lors de l’inscription de votre API web. Modifiez les lignes de code suivantes en remplaçant les valeurs par l’adresse de votre API web et les étendues exposées.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Ouvrez le fichier `authConfig.js` dans l’exemple.

4. Configurez l’exemple avec les paramètres que vous avez obtenus précédemment lors de l’inscription de votre application monopage. Modifiez les lignes de code suivantes en remplaçant les valeurs par votre ID client, vos métadonnées d’autorité et vos étendues de demande de jeton.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Pour plus d’informations, consultez cet [exemple d’application monopage JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :
- [Flux d’utilisateurs](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Personnalisation de l’expérience utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
