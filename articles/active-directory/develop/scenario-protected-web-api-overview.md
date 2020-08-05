---
title: Vue d’ensemble de l’API web protégée
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web protégée (vue d’ensemble).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026626"
---
# <a name="scenario-protected-web-api"></a>Scénario : API web protégée

Dans ce scénario, vous allez apprendre à exposer une API web. Vous allez également découvrir comment protéger une API web afin que seuls les utilisateurs authentifiés puissent y accéder.

Pour utiliser votre API web, vous devez soit autoriser les utilisateurs authentifiés disposant à la fois de comptes professionnels et scolaires, soit autoriser les comptes personnels Microsoft.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Spécificités

Voici des informations spécifiques que vous devez connaître pour protéger les API web :

- L’inscription de votre application doit exposer au moins une *étendue d’application* ou un *rôle d’application*.
  - Les étendues sont exposées par les API web appelées au nom d’un utilisateur.
  - Les rôles d’application sont exposés par les API web qui sont appelées par les applications démons (qui appellent votre API web en leur propre nom).
- Si vous créez une inscription d’application API web, choisissez la [version du jeton d’accès](reference-app-manifest.md#accesstokenacceptedversion-attribute) acceptée par votre API web pour `2`. Pour les API web héritées, la version du jeton acceptée peut être `null`. Toutefois, cette valeur limite l’audience de connexion aux organisations uniquement, et les comptes MSA (comptes Microsoft personnels) ne sont pas pris en charge.
- La configuration du code pour l’API web doit valider le jeton utilisé quand l’API web est appelée.
- Le code des actions de contrôleurs doit valider les rôles ou les étendues dans le jeton.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-protected-web-api-app-registration.md)
