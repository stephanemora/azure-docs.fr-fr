---
title: Démarrage rapide - Obtenir un jeton de relais réseau
description: Découvrez comment récupérer un jeton STUN/TURN à l’aide d’Azure Communication Services
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 05/21/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 2fc0bfeaef1d3e0e49d1597f41582b6d79d9a3db
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025918"
---
# <a name="quickstart-get-a-network-relay-token"></a>Démarrage rapide : obtenir un jeton de relais réseau

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Ce guide de démarrage rapide vous montre comment récupérer un jeton de relais réseau pour accéder aux serveurs TURN d’Azure Communication Services

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
