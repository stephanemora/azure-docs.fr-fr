---
title: Application Web qui se connecte les utilisateurs (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web qui connecte les utilisateurs (passer à la production)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074709"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Application Web qui connecte les utilisateurs - passer en production

Maintenant que vous savez comment acquérir un jeton pour appeler des API web, apprenez à déplacer vers la production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

### <a name="calling-web-apis-scenario"></a>Scénario d’API web appel

Une fois web se connecte en utilisateurs de votre application, elle peut appeler l’API web pour le compte d’utilisateurs connectés. Appeler des API web à partir de l’application web est l’objet du scénario suivant :

> [!div class="nextstepaction"]
> [Application Web appelle des API web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Approfondissement - didacticiel d’application web

En savoir plus sur les autres méthodes de connexion, les utilisateurs avec le didacticiel ASP.NET Core : [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Il s’agit d’un didacticiel progressif avec un code de production prêt pour une application web, y compris l’ajout de connexion.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
