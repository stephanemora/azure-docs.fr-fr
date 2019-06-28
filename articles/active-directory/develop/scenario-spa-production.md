---
title: Application monopage (passer en production) - Plateforme d’identité Microsoft
description: En savoir plus sur la création d’une application monopage (passer en production)
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075009"
---
# <a name="single-page-application---move-to-production"></a>Application monopage (passer en production)

Maintenant que vous savez comment acquérir un jeton pour appeler des API web, découvrez comment passer en production.

## <a name="improve-your-app"></a>Améliorer votre application

Suivez les étapes nécessaires pour préparer la production de votre application.

- [Activez la journalisation](msal-logging.md) dans votre application.

## <a name="test-your-integration"></a>Tester votre intégration

- Testez votre intégration en suivant la [check-list de l’intégration à la plateforme d’identités Microsoft](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Étapes suivantes

Voici quelques autres exemples/didacticiels :

- Pour une présentation approfondie d’un exemple de démarrage rapide expliquant le code pour savoir comment connecter des utilisateurs et obtenir un jeton d’accès pour appeler l’API Graph MS utilisant MSAL.js

    > [!div class="nextstepaction"]
    > [Didacticiel JavaScript SPA](./tutorial-v2-javascript-spa.md)

- Exemple qui montre comment obtenir des jetons pour vos propres API web de serveur principal à l’aide de MSAL.js

     > [!div class="nextstepaction"]
     > [SPA avec serveur principal ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Exemple montrant comment utiliser MSAL.js pour connecter des utilisateurs dans une application inscrite avec Azure AD B2C

    > [!div class="nextstepaction"]
    > [SPA avec Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
