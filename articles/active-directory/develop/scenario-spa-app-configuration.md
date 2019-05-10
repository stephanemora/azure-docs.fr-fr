---
title: Application à page unique (configuration de code de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (configuration de code de l’application)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406431"
---
# <a name="single-page-application---code-configuration"></a>Application à page unique - configuration du code

Découvrez comment configurer le code de votre application à page unique (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Bibliothèques MSAL prenant en charge les flux implicite

Plateforme d’identité Microsoft fournit la bibliothèque MSAL.js pour prendre en charge le flux implicite à l’aide de l’industrie recommandées sécurisé.  

Les bibliothèques prenant en charge les flux implicit sont :

| Bibliothèque MSAL | Description  |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Bibliothèque JavaScript simple à utiliser dans n’importe quelle application web de côté client créée à l’aide d’infrastructures JavaScript ou SPA comme Angular, Vue.js, React.js, etc. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper de la bibliothèque de MSAL.js principale pour simplifier l’utilisation dans les applications à page unique basé sur le framework Angular. Cette bibliothèque est disponible en version préliminaire et a [problèmes connus](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) avec certaines versions Angular et les navigateurs. |

## <a name="application-code-configuration"></a>Configuration de code d’application

Dans la bibliothèque MSAL, les informations d’inscription d’application sont passées en tant que configuration pendant l’initialisation de la bibliothèque.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Pour plus d’informations sur les options configurables disponibles, consultez [l’initialisation d’application avec MSAL.js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Connexion et déconnexion](scenario-spa-sign-in.md)
