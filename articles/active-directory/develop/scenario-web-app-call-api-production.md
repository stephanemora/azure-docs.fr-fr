---
title: Déplacer en production une application web appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment déplacer en production une application web appelant des API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675938"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Application web appelant des API web : passage en production

À présent que vous savez comment acquérir un jeton pour appeler des API web, voici quelques aspects à prendre en compte lors du déplacement de votre application vers la production.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage en suivant le tutoriel progressif complet sur les applications web ASP.NET Core. Le didacticiel :

- Montre comment connecter des utilisateurs à plusieurs audiences ou à des clouds nationaux, ou en utilisant des identités sociales.
- Appelle Microsoft Graph.
- Appelle plusieurs API Microsoft.
- Traite le consentement incrémentiel.
- Appelle votre propre API web.

[Tutoriel sur les applications web ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
