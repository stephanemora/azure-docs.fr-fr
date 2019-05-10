---
title: Découvrir l’intégration à Azure AD B2C à l’aide de Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) permet aux développeurs d’applications d’effectuer une intégration à Azure AD B2C et d’acquérir des jetons afin d’appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph, d’autres API Microsoft, des API web de tiers ou vos propres API web.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87bd8834f840c2246bf3adc1d1f9cd9b8f635915
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191019"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Intégrer Microsoft Authentication Library (MSAL) à Azure Active Directory B2C

Microsoft Authentication Library (MSAL) permet aux développeurs d’applications d’authentifier les utilisateurs avec des identités sociales et locales à l’aide d’[Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Azure Active Directory (Azure AD) B2C est un service de gestion des identités qui vous permet de personnaliser et contrôler la façon dont les clients s’inscrivent, se connectent et gèrent leurs profils lorsqu’ils utilisent vos applications.

Azure Active Directory (Azure AD) B2C vous permet également de personnaliser l’interface utilisateur (IU) de vos applications pour fournir une expérience fluide à votre client.

Ce tutoriel montre comment utiliser Microsoft Authentication Library (MSAL) pour effectuer une intégration à Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), créez-en un maintenant. Vous pouvez utiliser un locataire Azure AD B2C existant. 

## <a name="javascript"></a>JavaScript

Les étapes suivantes montrent comment une application monopage peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs, et pour appeler une API web protégée.

### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application

Pour implémenter une authentification, vous devez d’abord inscrire votre application. Pour ce faire, suivez [Inscrire votre application](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) afin d’obtenir des instructions détaillées.

### <a name="steps-2-download-applications"></a>Étape 2 : Téléchargez l’application

Téléchargez un fichier zip ou clonez l’exemple à partir de GitHub.
>git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Étape 3 : Authentication

1. Ouvrez le fichier index.html dans l’exemple.

2. Configurez l’exemple avec l’ID d’application et la clé que vous avez enregistrés lors de l’inscription de votre application. Modifiez les lignes de code suivantes en remplaçant les valeurs par les noms de votre répertoire et de vos API :

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Le nom de [flux d’utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) utilisé dans ce tutoriel est B2C_1_signupsignin1. Si vous utilisez un autre nom de flux d’utilisateur, indiquez-le dans la valeur authority.


### <a name="configure-application-to-use-b2clogincom"></a>Configurer l’application pour utiliser `b2clogin.com`

Vous pouvez utiliser `b2clogin.com` à la place de `login.microsoftonline.com` en guise d’URL de redirection quand vous configurez un fournisseur d’identité pour l’inscription et la connexion dans votre application Azure Active Directory (Azure AD) B2C.

**`b2clogin.com`**, c’est-à-dire 
`https://your-tenant-name.b2clogin.com/your-tenant-guid`, est utilisé pour les éléments suivants :

- L’espace utilisé dans l’en-tête de cookie par les services Microsoft est réduit.
- Vos URL n’incluent plus de référence à Microsoft. Par exemple, votre application Azure AD B2C fait probablement référence à login.microsoftonline.com.


 Pour utiliser « b2clogin.com », vous devez mettre à jour la configuration de votre application.  

1. Mettez à jour ValidateAuthority en définissant la propriété **validateAuthority** sur `false`. Quand **validateAuthority** a la valeur false, les redirections vers b2clogin.com sont autorisées.

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
> Votre application Azure AD B2C fait probablement référence à login.microsoftonline.com à plusieurs emplacements, par exemple au niveau des références de flux d’utilisateur ou des points de terminaison de jeton. Vérifiez que votre point de terminaison d’autorisation, votre point de terminaison de jeton et votre émetteur ont été mis à jour pour utiliser your-tenant-name.b2clogin.com.

Suivez cet [exemple MSAL pour JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) qui montre comment utiliser la préversion de Microsoft Authentication Library pour JavaScript (msal.js) afin d’obtenir un jeton d’accès et d’appeler une API sécurisée par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personnalisation de l’interface utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)