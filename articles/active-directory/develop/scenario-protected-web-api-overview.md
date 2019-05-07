---
title: Protégé par les API Web - vue d’ensemble | Azure
description: Découvrez comment créer un site web protégé API (vue d’ensemble).
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
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074889"
---
# <a name="scenario-protected-web-api"></a>Scénario : API web protégée

Dans ce scénario, nous allons vous montrer comment vous pouvez exposer une API web et comment vous pouvez le protéger afin que seuls les utilisateurs authentifiés permettre accéder à l’API. Vous souhaitez autoriser les utilisateurs authentifiés avec à la fois travail et comptes scolaires ou des comptes personnels Microsoft personnels à utiliser votre API web.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Spécificités

Voici certaines caractéristiques que vous devez savoir pour protéger les API web :

- L’inscription de votre application doit exposer au moins une étendue. La version de jeton acceptée par votre API web dépend de l’audience de connexion.
- La configuration du code pour l’API web doit valider le jeton qui est utilisé lors de l’appel de l’API web.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-protected-web-api-app-registration.md)
