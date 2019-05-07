---
title: Application à page unique (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (passer à la production)
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075009"
---
# <a name="single-page-application---move-to-production"></a>Application à page unique - déplacement en production

Maintenant que vous savez comment acquérir un jeton pour appeler les API Web, découvrez comment passer en production.

## <a name="improve-your-app"></a>Améliorer votre application

Suivez les étapes nécessaires pour préparer la production de votre application.

- [Activer la journalisation](msal-logging.md) dans votre application.

## <a name="test-your-integration"></a>Tester votre intégration

- Tester votre intégration en suivant le [liste de vérification de l’intégration à plateforme Microsoft identity](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Étapes suivantes

Voici quelques autres exemples/didacticiels :

- Pour une présentation approfondie du démarrage rapide exemple qui explique le code pour savoir comment connecter des utilisateurs et obtenir un jeton d’accès pour appeler l’API Graph MS utilisant MSAL.js

    > [!div class="nextstepaction"]
    > [Didacticiel d’application SPA JavaScript](./tutorial-v2-javascript-spa.md)

- Exemple qui montre comment obtenir des jetons pour vos propres API web de serveur principal à l’aide de MSAL.js

     > [!div class="nextstepaction"]
     > [SPA avec un serveur principal ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Exemple de montrer comment utiliser MSAL.js pour connecter les utilisateurs dans une application inscrite avec Azure AD B2C

    > [!div class="nextstepaction"]
    > [SPA avec Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
