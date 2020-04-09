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
ms.openlocfilehash: 9017fbde1a44bcdf39fa8730bed25141da19fd56
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882401"
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
