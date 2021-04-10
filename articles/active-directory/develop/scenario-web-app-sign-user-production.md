---
title: Déplacer l’application web qui connecte les utilisateurs en production | Azure
titleSuffix: Microsoft identity platform
description: Apprenez à générer une application web qui connecte des utilisateurs (passage en production)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f670af1fca4b4638988e53075f092ca1bbac55b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578258"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Application web qui connecte les utilisateurs : Passer en production

À présent que vous savez comment obtenir un jeton pour appeler des API web, voici quelques aspects à prendre en compte lors du déplacement de votre application vers la production.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Dépannage
Lorsque les utilisateurs se connectent à l’application web pour la première fois, ils doivent donner leur consentement. Toutefois, dans certaines organisations, les utilisateurs peuvent voir un message similaire à ce qui suit : Pour accéder aux ressources de votre organisation, *AppName a besoin d’autorisations que seul un administrateur peut accorder. Veuillez demander à un administrateur d’accorder une autorisation pour cette application avant de pouvoir l’utiliser.*
Cela est dû au fait que votre administrateur client a **désactivé** la possibilité pour les utilisateurs de donner leur consentement. Dans ce cas, contactez vos administrateurs clients pour qu’ils donnent leur consentement d’administrateur pour les étendues requises par l’application.

## <a name="same-site"></a>Même site

Vérifiez que vous comprenez les problèmes susceptibles d’affecter les nouvelles versions du navigateur Chrome : [Guide pratique pour gérer les changements de cookie SameSite dans le navigateur Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md).

Le package NuGet Microsoft.Identity.Web gère les problèmes SameSite les plus courants.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Présentation approfondie : Didacticiel sur les applications web ASP.NET Core

En savoir plus sur les autres façons de connecter les utilisateurs avec ce didacticiel ASP.NET Core : 

[Permettez à vos applications web de connecter des utilisateurs et d’appeler des API avec la plateforme d’identité Microsoft pour les développeurs](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Ce didacticiel progressif propose un code prêt pour la production d’une application web, avec la procédure d’ajout de connexion aux comptes dans :

- votre organisation
- plusieurs organisations
- des comptes professionnels et scolaires ou des comptes Microsoft personnels
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Clouds nationaux

## <a name="tutorial-nodejs-web-app"></a>Tutoriel : application web Node.js

En savoir plus sur le Web Node.js dans ce tutoriel :

[Tutoriel : Connecter des utilisateurs dans une application web Node.js & Express](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-nodejs-webapp-msal)

## <a name="sample-code-java-web-app"></a>Exemple de code : Applications web Java

En savoir plus sur l’application web Java à partir de cet exemple sur GitHub : 

[Une application web Java qui connecte les utilisateurs à la plateforme d’identités Microsoft et appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Étapes suivantes

Lorsque votre application web connecte des utilisateurs, elle peut appeler des API web pour le compte des utilisateurs connectés. L’appel d’API web à partir de l’application web est l’objet du scénario suivant : [Application web qui appelle des API web](scenario-web-app-call-api-overview.md).