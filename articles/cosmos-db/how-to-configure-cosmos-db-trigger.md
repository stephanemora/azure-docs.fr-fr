---
title: Déclencheur Azure Functions pour Cosmos DB configuration avancée
description: Découvrez comment configurer la journalisation et la stratégie de connexion utilisées par le déclencheur Azure Functions pour Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 30328db465e0d9bf8c1ce67d92e48c688c51e043
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574624"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Comment configurer la journalisation et la connectivité avec le déclencheur Azure Functions pour Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cet article décrit les options de configuration avancées que vous pouvez définir lors de l’utilisation du déclencheur Azure Functions pour Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Activation des journaux spécifiques aux déclencheurs

Le déclencheur Azure Functions pour Cosmos DB utilise la [bibliothèque du processeur de flux de modification](./change-feed-processor.md) en interne, qui génère plusieurs journaux de contrôle de l’intégrité vous permettant de superviser les opérations internes dans le cadre du [dépannage](./troubleshoot-changefeed-functions.md).

Les journaux de contrôle de l’intégrité décrivent le comportement du déclencheur Azure Functions pour Cosmos DB lors des tentatives d’opérations dans des scénarios d’équilibrage de charge ou d’initialisation.

### <a name="enabling-logging"></a>Activation de la journalisation

Pour activer la journalisation en cas d’utilisation du déclencheur Azure Functions pour Cosmos DB, recherchez le fichier `host.json` dans votre projet ou application Azure Functions et [configurez le niveau de journalisation nécessaire](../azure-functions/functions-monitoring.md#log-levels-and-categories). Activez les traces pour  `Host.Triggers.CosmosDB` comme indiqué dans l’exemple suivant :

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Une fois qu’Azure Functions a été déployé avec la configuration mise à jour, les journaux du déclencheur Azure Functions pour Cosmos DB sont inclus dans les traces. Vous pouvez consulter les journaux dans le fournisseur de journalisation configuré sous *Category* `Host.Triggers.CosmosDB`.

### <a name="query-the-logs"></a>Interroger les journaux

Exécutez la requête suivante pour interroger les journaux générés par le déclencheur Azure Functions pour Cosmos DB dans l’outil [Azure Application Insights Analytics](../azure-monitor/logs/log-query-overview.md) :

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Configuration de la stratégie de connexion

Il existe deux modes de connexion : le mode direct et le mode passerelle. Pour en savoir plus sur ces modes de connexion, consultez l’article [modes de connexion](sql-sdk-connection-modes.md) . Par défaut, le mode **passerelle** est utilisé pour établir toutes les connexions sur le déclencheur Azure Functions pour Cosmos DB. Toutefois, il ne s’agit peut-être pas de la meilleure option pour les scénarios basés sur les performances.

### <a name="changing-the-connection-mode-and-protocol"></a>Modification du mode et du protocole de connexion

Il existe deux paramètres de configuration essentiels pour la stratégie de connexion client : le **mode de connexion** et le **protocole de connexion**. Vous pouvez changer le mode et le protocole de connexion par défaut utilisés par le déclencheur Azure Functions pour Cosmos DB et toutes les [liaisons Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Pour modifier les paramètres par défaut, vous avez besoin de trouver le fichier `host.json` dans votre projet ou application Azure Functions, puis d’ajouter le [paramètre supplémentaire](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) suivant :

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
> Lorsque vous hébergez votre application de fonction dans un plan Consommation, chaque instance comporte une limite du nombre de connexions de socket qu’elle peut gérer. Quand vous utilisez le mode direct/TCP, à dessein, un nombre plus élevé de connexions est créé et peut atteindre la [limite du plan Consommation](../azure-functions/manage-connections.md#connection-limit), auquel cas vous pouvez utiliser le mode passerelle ou héberger votre application de fonction dans un [plan Premium](../azure-functions/functions-premium-plan.md) ou un [plan Dedicated (App Service)](../azure-functions/dedicated-plan.md).

## <a name="next-steps"></a>Étapes suivantes

* [Limites de connexions dans Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Activez la supervision](../azure-functions/functions-monitoring.md) dans vos applications Azure Functions.
* Découvrez comment [diagnostiquer et résoudre les problèmes courants](./troubleshoot-changefeed-functions.md) quand vous utilisez le déclencheur Azure Functions pour Cosmos DB.