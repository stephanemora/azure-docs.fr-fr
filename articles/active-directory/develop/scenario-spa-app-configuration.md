---
title: Application monopage (configuration du code de l’application) - Plateforme d’identité Microsoft
description: En savoir plus sur la création d’une application monopage (configuration du code de l’application)
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
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891531"
---
# <a name="single-page-application---code-configuration"></a>Application monopage - Configuration du code

En savoir plus sur la configuration du code de votre application monopage (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Bibliothèques MSAL prenant en charge le flux implicite

Plateforme d’identité Microsoft fournit la bibliothèque MSAL.js pour prendre en charge le flux implicite à l’aide des pratiques sécurisées recommandées du secteur d’activité.  

Les bibliothèques MSAL prenant en charge le flux implicite sont les suivantes :

| Bibliothèque MSAL | Description |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Bibliothèque JavaScript brute à utiliser dans n’importe quelle application web côté client créée à l’aide d’infrastructures JavaScript ou SPA telles que Angular, Vue.js, React.js, etc. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper de la bibliothèque MSAL.js principale pour simplifier l’utilisation dans les applications monopages créées à l’aide de l’infrastructure Angular. Cette bibliothèque est disponible en préversion et comporte des [problèmes connus](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) avec certaines versions d’Angular et navigateurs. |

## <a name="application-code-configuration"></a>Configuration du code de l’application

Dans la bibliothèque MSAL, les informations d’inscription d’application sont transmises en tant que configuration pendant l’initialisation de la bibliothèque.

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
Pour plus d’informations sur les options configurables disponibles, consultez [Initializing application with MSAL.js](msal-js-initializing-client-applications.md) (Initialisation d’application avec MSAL.js).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter et se déconnecter](scenario-spa-sign-in.md)
