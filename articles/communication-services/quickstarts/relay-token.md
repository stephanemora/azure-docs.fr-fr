---
title: 'Démarrage rapide : Accès aux relais TURN'
description: Découvrez comment récupérer un jeton STUN/TURN à l’aide d’Azure Communication Services
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 88bdfe85dbd2905ff060498003b23909abe7a855
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471431"
---
# <a name="quickstart-access-turn-relays"></a>Démarrage rapide : Accès aux relais TURN

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Ce guide de démarrage rapide vous montre comment récupérer un jeton de relais réseau pour accéder aux serveurs TURN d’Azure Communication Services.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
- Une ressource Azure Communication Services active ou consultez [Créer une ressource Communication Services](./create-communication-resource.md) si vous n’en avez pas.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer une ressource Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](./create-communication-resource.md#clean-up-resources).
