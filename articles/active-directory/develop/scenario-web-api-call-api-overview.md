---
title: Créer une API web appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Apprenez à créer une API web qui appelle des API web en aval (vue d'ensemble).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 376c61f6a5ba94492cac26950465c61e3d8fe4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038558"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scénario : Une API web qui appelle des API web

Découvrez tout ce que vous devez savoir pour générer une API web qui appelle des API web.

## <a name="prerequisites"></a>Prérequis

Ce scénario, dans lequel une API web protégée appelle d’autres API web, repose sur le [scénario : API web protégée](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Vue d’ensemble

- Un client d’application web, de bureau, mobile ou monopage (non représenté sur le diagramme) appelle une API web protégée et fournit un jeton du porteur JSON Web Token (JWT) dans son en-tête HTTP « Autorisation ».
- L’API web protégée valide le jeton et utilise la méthode de la bibliothèque d’authentification Microsoft (MSAL) `AcquireTokenOnBehalfOf` pour demander un autre jeton à Azure Active Directory (Azure AD) afin que l’API web protégée puisse appeler une deuxième API web, ou une API web en aval, pour le compte de l’utilisateur. `AcquireTokenOnBehalfOf` actualise le jeton, si besoin.
![Diagramme d’une API web appelant une API web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Spécificités

La partie de l’inscription d’application liée aux autorisations de l’API est classique. La configuration de l'application implique l’utilisation du flux OBO OAuth 2.0 afin d'échanger le jeton du porteur JWT contre un jeton pour une API en aval. Ce jeton est ajouté au cache de jetons, où il est disponible dans les contrôleurs des API web, et peut ensuite obtenir un jeton en mode silencieux pour appeler des API en aval.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Inscription d’application](scenario-web-api-call-api-app-registration.md).
