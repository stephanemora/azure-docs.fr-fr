---
title: Stratégie de connexion de déclencheur Azure Cosmos DB
description: Découvrir comment configurer la stratégie de connexion utilisée par le déclencheur Azure Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755457"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Guide pratique pour configurer la stratégie de connexion utilisée par le déclencheur Azure Cosmos DB

Cet article décrit comment configurer la stratégie de connexion quand vous utilisez le déclencheur Azure Cosmos DB pour vous connecter à votre compte Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Pourquoi la stratégie de connexion est-elle importante ?

Il existe deux modes de connexion : le mode direct et le mode passerelle. Pour en savoir plus sur ces modes de connexion, consultez l’article sur les [conseils relatifs aux performances](./performance-tips.md#networking). Par défaut, le mode **passerelle** est utilisé pour établir toutes les connexions sur le déclencheur Azure Cosmos DB. Toutefois, il ne s’agit peut-être pas de la meilleure option pour les scénarios basés sur les performances.

## <a name="changing-the-connection-mode-and-protocol"></a>Modification du mode et du protocole de connexion

Il existe deux paramètres de configuration essentiels pour la stratégie de connexion client : le **mode de connexion** et le **protocole de connexion**. Vous pouvez modifier le mode et le protocole de connexion par défaut utilisés par le déclencheur Azure Cosmos DB et toutes les [liaisons Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Pour modifier les paramètres par défaut, vous avez besoin de trouver le fichier `host.json` dans votre projet ou application Azure Functions, puis d’ajouter le [paramètre supplémentaire](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) suivant :

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Où `connectionMode` doit avoir le mode de connexion souhaité (direct ou passerelle) et `protocol` le protocole de connexion souhaité (Tcp ou Https). 

Si votre projet Azure Functions utilise le runtime Azure Functions V1, la configuration porte un nom légèrement différent. Vous devez utiliser `documentDB` au lieu de `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Quand vous utilisez le plan d’hébergement d’un plan de consommation Azure Functions, chaque instance comporte une limite du nombre de connexions de socket qu’elle peut gérer. Quand vous utilisez le mode direct/TCP, à dessein, un nombre plus élevé de connexions est créé et peut atteindre la [limite du plan de consommation](../azure-functions/manage-connections.md#connection-limit), auquel cas vous pouvez utiliser le mode passerelle ou exécuter vos fonctions Azure en [mode App Service](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Étapes suivantes

* [Limites de connexions dans Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Conseils sur les performances Azure Cosmos DB](./performance-tips.md)
* [Exemples de code](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
