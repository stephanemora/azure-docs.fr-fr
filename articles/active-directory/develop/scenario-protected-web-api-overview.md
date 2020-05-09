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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537200"
---
# <a name="scenario-protected-web-api"></a>Scénario : API web protégée

Dans ce scénario, vous allez apprendre à exposer une API web. Vous allez également découvrir comment protéger une API web afin que seuls les utilisateurs authentifiés puissent y accéder.

Pour utiliser votre API web, vous devez soit autoriser les utilisateurs authentifiés disposant à la fois de comptes professionnels et scolaires, soit autoriser les comptes personnels Microsoft.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Spécificités

Voici des informations spécifiques que vous devez connaître pour protéger les API web :

- L’inscription de votre application doit exposer au moins une étendue. La version de jeton acceptée par votre API web dépend de l’audience de connexion.
- La configuration du code pour l’API web doit valider le jeton utilisé quand l’API web est appelée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-protected-web-api-app-registration.md)
