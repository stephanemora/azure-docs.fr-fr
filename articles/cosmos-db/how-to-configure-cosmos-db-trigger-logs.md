---
title: Journaux du déclencheur Azure Cosmos DB
description: Découvrez comment exposer les journaux du déclencheur Azure Cosmos DB à votre pipeline de journalisation Azure Functions
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515017"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Guide pratique pour configurer et lire les journaux du déclencheur Azure Cosmos DB

Cet article explique comment configurer votre environnement Azure Functions pour envoyer les journaux du déclencheur Azure Cosmos DB à la [solution de supervision](../azure-functions/functions-monitoring.md) que vous avez configurée.

## <a name="included-logs"></a>Journaux inclus

Le déclencheur Azure Cosmos DB utilise la [bibliothèque du processeur de flux de modification](./change-feed-processor.md) en interne, laquelle génère plusieurs journaux de contrôle de l’intégrité qui vous permettent de superviser les opérations internes dans le cadre du [dépannage](./troubleshoot-changefeed-functions.md).

Les journaux de contrôle de l’intégrité décrivent le comportement du déclencheur Azure Cosmos DB lors des tentatives d’opérations durant l’équilibrage de charge ou l’initialisation.

## <a name="enabling-logging"></a>Activation de la journalisation

Pour activer la journalisation du déclencheur Azure Cosmos DB, recherchez le fichier `host.json` dans votre projet ou application Azure Functions et [configurez le niveau de journalisation nécessaire](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Vous devez activer les traces pour `Host.Triggers.CosmosDB` comme indiqué dans l’exemple suivant :

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

Une fois qu’Azure Functions a été déployé avec la configuration mise à jour, les journaux du déclencheur Azure Cosmos DB sont inclus dans les traces. Vous pouvez consulter les journaux dans le fournisseur de journalisation configuré sous *Category* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Interroger les journaux

Exécutez la requête suivante pour interroger les journaux générés par le déclencheur Azure Cosmos DB dans l’outil [Azure Application Insights Analytics](../azure-monitor/app/analytics.md) :

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Étapes suivantes

* [Activez la supervision](../azure-functions/functions-monitoring.md) dans vos applications Azure Functions.
* Découvrez comment [diagnostiquer et résoudre les problèmes courants](./troubleshoot-changefeed-functions.md) quand vous utilisez le déclencheur Azure Cosmos DB dans Azure Functions.