---
title: Fonctionnalités Durable Functions en version préliminaire - Azure Functions
description: Découvrez les fonctionnalités de Durable Functions en version préliminaire.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933244"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Préversion de Durable Functions 2.0 (Azure Functions)

*Fonctions durables* est une extension d[’Azure Functions](../functions-overview.md) et d[’Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) qui vous permet d’écrire des fonctions avec état dans un environnement sans serveur. L’extension gère l’état, les points de contrôle et les redémarrages à votre place. Si vous n’êtes pas encore familiarisé avec Durable Functions, consultez la [documentation générale](durable-functions-overview.md).

Durable Functions 1.x est une fonctionnalité mise à la disposition générale d’Azure Functions, mais elle contient également plusieurs sous-composants qui sont actuellement en version préliminaire publique. Cet article décrit les fonctionnalités qui viennent d’être lancées en version préliminaire et se penche sur les détails de leur fonctionnement et la façon dont vous pouvez commencer à les utiliser.

> [!NOTE]
> Ces fonctionnalités de préversion font partie de Durable Functions version 2.0, qui est actuellement en **qualité de préversion de mise en production** avec plusieurs changements cassants. Les versions du package d’extension Azure Durable Functions sont accessibles sur nuget.org avec les versions au format forme de **2.0.0-betaX**. Ces versions ne conviennent pas aux charges de travail de production, et les versions ultérieures pourraient contenir des changements cassants supplémentaires.

## <a name="breaking-changes"></a>Dernières modifications

Plusieurs changements cassants sont introduits dans Durable Functions 2.0. Les applications existantes ne devraient pas être compatibles avec Durable Functions 2.0 sans modification du code. Cette section répertorie certaines de ces modifications :

### <a name="hostjson-schema"></a>Schéma Host.json

L’extrait de code suivant montre le nouveau schéma host.json. Les principales modifications à connaître sont les nouvelles sous-sections :

* `"storageProvider"` (et la sous-section `"azureStorage"`) pour la configuration spécifique au stockage
* `"tracking"` pour la configuration du suivi et de la journalisation
* `"notifications"` (et la sous-section `"eventGrid"`) pour la configuration de notification de grille d’événement

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Quand Durable Functions 2.0 continuera à se stabiliser, d’autres modifications seront apportées à la section `durableTask` de host.json. Pour plus d’informations sur ces modifications, consultez [ce problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Modifications de l’interface publique

Les divers objets de « contexte » pris en charge par Durable Functions avaient des classes abstraites prévues pour une utilisation lors des tests unitaires. Dans le cadre de Durable Functions 2.0, ces classes de base abstraites ont été remplacées par des interfaces. Le code de fonction utilisant les types concrets directement ne sera pas affecté.

Le tableau suivant représente les principales modifications :

| Ancien type | Nouveau type |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

Dans le cas où une classe de base abstraite contenait des méthodes virtuelles, ces méthodes virtuelles ont été remplacées par les méthodes d’extension définies dans `DurableContextExtensions`.

## <a name="entity-functions"></a>Fonctions d’entité

À partir de Durable Functions v2.0.0-alpha, nous avons introduit un nouveau concept de [fonctions d’entité](durable-functions-entities.md).

Les fonctions d’entité définissent les opérations pour la lecture et la mise à jour de petits éléments d’état, connus sous le nom *d’entités durables*. Comme les fonctions d’orchestrateur, les fonctions d’entité sont des fonctions ayant un type spécial de déclencheur, *déclencheur d’entité*. Contrairement aux fonctions d’orchestrateur, les fonctions d’entité n’ont pas de contraintes code spécifiques. Les fonctions d’entité gèrent également l’état explicitement plutôt que de représenter implicitement l’état via le flux de contrôle.

Sur la base des commentaires initiaux des utilisateurs, nous avons ajouté ultérieurement la prise en charge d’un modèle de programmation basé sur les classes pour les entités dans Durable Functions v2.0.0-beta1.

Pour plus d’informations, consultez cet article sur les [fonctions d’entité](durable-functions-entities.md).

## <a name="durable-http"></a>Fonctionnalité HTTP durable

À partir de Durable Functions v2.0.0-beta2, nous avons introduit une nouvelle fonctionnalité [HTTP durable](durable-functions-http-features.md) qui vous permet d’effectuer les opérations suivantes :

* Appeler les API HTTP directement à partir des fonctions d’orchestration (avec certaines limitations documentées)
* Implémenter une interrogation d’état HTTP 202 automatique côté client
* Bénéficier d’une prise en charge intégrée des [identités managées Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Pour plus d’informations, consultez cet article sur les [fonctionnalités HTTP](durable-functions-http-features.md#consuming-http-apis).

## <a name="alternate-storage-providers"></a>Autres fournisseurs de stockage

L’infrastructure Durable Task prend en charge plusieurs fournisseurs de stockage, dont [Stockage Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), un [en émulateur en mémoire](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)et un fournisseur [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) expérimental. Toutefois, jusqu'à présent, l’extension Durable Task pour Azure Functions prenait uniquement en charge le fournisseur Stockage Azure. À compter de Durable Functions 2.0, la prise en charge d’autres fournisseurs de stockage est ajoutée, en commençant par le fournisseur Redis.

> [!NOTE]
> Durable Functions 2.0 prend uniquement en charge les fournisseurs compatibles .NET standard 2.0.

### <a name="emulator"></a>Émulateur

Le fournisseur [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) est un fournisseur de stockage en mémoire locale, non durable idéal pour les scénarios de tests locaux. Il peut être configuré à l’aide du schéma **host.json** minimal suivant :

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (expérimental)

Le fournisseur [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) fait persister tous les états d’orchestration dans un cluster Redis configuré.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

Le `connectionStringName` doit référencer le nom d’une variable d’environnement ou d’un paramètre d’application. Ce paramètre d’application ou cette variable d’environnement doit contenir une valeur de chaîne de connexion Redis au format *serveur:port*. Par exemple, `localhost:6379` pour la connexion à un cluster Redis local.

> [!NOTE]
> Le fournisseur Redis est actuellement en phase expérimentale et prend uniquement en charge les applications de fonction s’exécutant sur un nœud unique. Il n’est pas garanti que le fournisseur Redis sera mis à la disposition générale et qu’il pourra être supprimé dans une version ultérieure.
