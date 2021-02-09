---
title: Déplacer une application monopage vers la production
titleSuffix: Microsoft identity platform
description: En savoir plus sur la création d’une application monopage (passer en production)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954956"
---
# <a name="single-page-application-move-to-production"></a>Application monopage : Passer en production

À présent que vous savez comment acquérir un jeton pour appeler des API web, voici quelques aspects à prendre en compte lors du déplacement de votre application vers la production.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Déployer votre application

Consultez un [exemple de déploiement](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) pour apprendre à déployer vos projets de SPA et d’API web respectivement avec le service Stockage Azure et Azure App Services. 

## <a name="code-samples"></a>Exemples de code

Ces exemples de code illustrent plusieurs opérations clés pour une application monopage.
- [Application monopage avec serveur principal ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) : comment obtenir des jetons pour votre propre API web de serveur principal (ASP.NET Core) à l’aide de **MSAL.js**.

- [API web Node.js (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) : comment valider des jetons d’accès pour votre API web de serveur principal (Node.js) à l’aide de **passport-azure-ad**.

- [Application monopage avec Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) : comment utiliser **MSAL.js** pour connecter des utilisateurs dans une application inscrite auprès d’**Azure Active Directory B2C** (Azure AD B2C).

- [API web Node.js (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) : comment utiliser **passport-azure-ad** pour valider des jetons d’accès pour des applications inscrites auprès d’**Azure Active Directory B2C** (Azure AD B2C).

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel Application monopage JavaScript](./tutorial-v2-javascript-spa.md) : présentation approfondie de la connexion d’utilisateurs et de l’obtention d’un jeton d’accès pour appeler l’**API Microsoft Graph** à l’aide de **MSAL.js**.