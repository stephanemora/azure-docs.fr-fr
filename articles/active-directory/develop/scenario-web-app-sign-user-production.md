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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074709"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Application Web qui connecte les utilisateurs - Passage en production

Maintenant que vous savez comment acquérir un jeton pour appeler des API web, découvrez comment passer en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

### <a name="calling-web-apis-scenario"></a>Scénario d’appel d’API web

Lorsque votre application web connecte des utilisateurs, elle peut appeler des API web pour le compte des utilisateurs connectés. L’appel d’API web à partir de l’application web est l’objet du scénario suivant :

> [!div class="nextstepaction"]
> [Application web qui appelle des API web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Approfondissement - Didacticiel d’application web

Découvrez d’autres méthodes de connexion d’utilisateurs dans le didacticiel ASP.NET Core : [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Il s’agit d’un didacticiel progressif contenant un code prêt pour la production pour une application web, y compris la procédure d’ajout de connexion.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
