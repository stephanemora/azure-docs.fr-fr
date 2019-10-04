---
title: API web qui appelle des API web en aval (vue d'ensemble) - Plateforme d’identités Microsoft
description: Apprenez à créer une API web qui appelle des API web en aval (vue d'ensemble).
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852499"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scénario : API web qui appelle des API web

Découvrez tout ce que vous devez savoir pour générer une API web qui appelle des API web.

## <a name="prerequisites"></a>Prérequis

Ce scénario, une API web protégée appelant des API web, repose sur le scénario « Protéger une API web ». Pour en savoir plus sur ce scénario de base, consultez dans un premier temps [API web protégée - Scénario](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Vue d'ensemble

- Un client (application web, bureau, mobile ou monopage), non représenté sur le diagramme ci-dessous, appelle une API web protégée et fournit un jeton de porteur JWT dans son en-tête HTTP « Autorisation ».
- L'API web protégée valide le jeton et utilise la méthode MSAL `AcquireTokenOnBehalfOf` pour demander (à Azure AD) un autre jeton afin d'appeler lui-même une deuxième API web (appelée API web en aval) pour le compte de l’utilisateur.
- L’API web protégée utilise ce jeton pour appeler une API en aval. Elle peut également l'appeler `AcquireTokenSilent`ultérieurement afin de demander des jetons pour les autres API en aval (mais toujours pour le compte du même utilisateur). `AcquireTokenSilent` actualise le jeton, si besoin.

![API web appelant une API web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Spécificités

La partie de l’inscription d’application liée aux autorisations de l’API est classique. La configuration de l'application implique l’utilisation du flux OBO OAuth 2.0 afin d'échanger le jeton de porteur JWT contre un jeton pour une API en aval. Ce jeton est ajouté au cache de jetons, où il est disponible dans les contrôleurs des API web, et peut obtenir un jeton en mode silencieux pour appeler des API en aval.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-web-api-call-api-app-registration.md)
