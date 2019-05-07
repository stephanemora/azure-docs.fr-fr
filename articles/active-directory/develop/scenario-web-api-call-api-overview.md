---
title: API Web qui appelle en aval web API (vue d’ensemble) - plateforme d’identité Microsoft
description: Découvrez comment générer une API qui appelle en aval des API web (vue d’ensemble) web.
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
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075399"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scénario : API Web qui appelle des API web

Découvrez toutes les que nécessaires à la création d’une API web qui appelle des API web.

## <a name="prerequisites"></a>Conditions préalables

Ce scénario, les API web protégée par les appels de web API, s’appuie sur le scénario de « Protéger une API web ». Pour en savoir plus sur ce scénario fondamentaux, consultez [API Web protégée par - scénario](scenario-protected-web-api-overview.md) première.

## <a name="overview"></a>Présentation

- Un client (web, bureau, application mobile ou monopage) - ne pas représenté dans le diagramme ci-dessous - appelle une API web protégée et fournit un jeton de porteur JWT dans son en-tête « Authorization » Http.
- Valide le jeton de l’API web protégée et utilise la bibliothèque MSAL `AcquireTokenOnBehalfOf` appel de méthode pour demander un autre jeton (à partir d’Azure AD) afin qu’il puisse, lui-même, une deuxième web API (nommée l’API web en aval) pour le compte de l’utilisateur.
- L’API web protégée utilise ce jeton pour appeler une API en aval. Elle peut également appeler `AcquireTokenSilent`par la suite pour demander des jetons pour les autres API en aval (mais toujours au nom de l’utilisateur même). `AcquireTokenSilent` Actualise le jeton si nécessaire.

![Appeler une API web des API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Spécificités

La partie de l’inscription de l’application liée aux autorisations de l’API est classique. La configuration d’application implique l’utilisation du flux d’authentification OAuth 2.0 on-behalf-of pour échanger le jeton de porteur JWT contre un jeton pour une API en aval. Ce jeton est ajouté au cache du jeton, où il est disponible dans les contrôleurs de l’API web et peut acquérir un jeton en mode silencieux pour appeler des API en aval.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-web-api-call-api-app-registration.md)
