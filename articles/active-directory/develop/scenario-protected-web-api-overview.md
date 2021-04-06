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
ms.openlocfilehash: 434c255f0b9634e0d17bad1627ddd92af9498bfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582432"
---
# <a name="scenario-protected-web-api"></a>Scénario : API web protégée

Dans ce scénario, vous allez apprendre à exposer une API web. Vous allez également découvrir comment protéger une API web afin que seuls les utilisateurs authentifiés puissent y accéder.

Pour utiliser votre API web, vous autorisez les utilisateurs authentifiés disposant à la fois de comptes professionnels et scolaires, ou vous autorisez les comptes personnels Microsoft.

## <a name="specifics"></a>Spécificités

Voici des informations spécifiques que vous devez connaître pour protéger les API web :

- L’inscription de votre application doit exposer au moins une *étendue d’application* ou un *rôle d’application*.
  - Les étendues sont exposées par les API web appelées au nom d’un utilisateur.
  - Les rôles d’application sont exposés par les API web qui sont appelées par les applications démons (qui appellent votre API web en leur propre nom).
- Si vous créez une inscription d’application API web, choisissez la [version du jeton d’accès](reference-app-manifest.md#accesstokenacceptedversion-attribute) acceptée par votre API web pour `2`. Pour les API web héritées, la version du jeton acceptée peut être `null`. Toutefois, cette valeur limite l’audience de connexion aux organisations uniquement, et les comptes MSA (comptes Microsoft personnels) ne sont pas pris en charge.
- La configuration du code pour l’API web doit valider le jeton utilisé quand l’API web est appelée.
- Le code des actions de contrôleurs doit valider les rôles ou les étendues dans le jeton.

## <a name="recommended-reading"></a>Lectures recommandées

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Inscription d’application](scenario-protected-web-api-app-registration.md).
