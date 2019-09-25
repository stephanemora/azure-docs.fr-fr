---
title: Application web connectant des utilisateurs (passage en production) - Plateforme d’identité Microsoft
description: Apprenez à générer une application web qui connecte des utilisateurs (passage en production)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086547"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Application Web qui connecte les utilisateurs - Passage en production

Maintenant que vous savez comment acquérir un jeton pour appeler des API web, découvrez comment passer en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

### <a name="calling-web-apis-scenario"></a>Scénario d’appel d’API web

Lorsque votre application web connecte des utilisateurs, elle peut appeler des API web pour le compte des utilisateurs connectés. L’appel d’API web à partir de l’application web est l’objet du scénario suivant :

> [!div class="nextstepaction"]
> [Application web qui appelle des API web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Présentation approfondie - Tutoriel sur les applications web ASP.NET Core

Découvrez d’autres méthodes de connexion d’utilisateurs dans le tutoriel ASP.NET Core : [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Cet exemple constitue un tutoriel graduel contenant un code prêt pour la production d’une application web, avec la procédure d’ajout de connexion aux comptes dans :

- votre organisation
- plusieurs organisations,
- des comptes professionnels et scolaires, ou des comptes Microsoft personnels,
- avec [Azure AD B2C](https://aka.ms/aadb2c)
- ou dans les clouds nationaux.

### <a name="sample-code---java-web-app"></a>Exemple de code - Application web Java

Apprenez-en davantage sur l’application web Java à partir de l’exemple sur GitHub : [Une application web Java qui connecte les utilisateurs à la plateforme d’identités Microsoft et appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
