---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 32494c60e3189c9369bafe37289d01e248e4a678
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122264649"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|Propriété  |Default | Description |
|---------|---------|---------|
|batchCheckpointFrequency|1|Nombre de lots d’événements à traiter avant de créer un point de contrôle de curseur EventHub.|
|eventProcessorOptions/maxBatchSize|10|Nombre d’événements maximal reçu par boucle de réception.|
|eventProcessorOptions/prefetchCount|300|Nombre de prérécupérations par défaut utilisé par le `EventProcessorHost` sous-jacent. La valeur minimale autorisée est de 10.|
|initialOffsetOptions/type<sup>1</sup>|fromStart|Emplacement dans le flux d’événements à partir duquel démarrer le traitement lorsqu’il n’existe pas de point de contrôle dans le stockage. Les options sont `fromStart`, `fromEnd` ou `fromEnqueuedTime`. `fromEnd` traite les nouveaux événements qui ont été mis en file d’attente après le début de l’exécution de l’application de fonction. S’applique à toutes les partitions.  Pour plus d’informations, consultez la [documentation sur EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|
|initialOffsetOptions/enqueuedTimeUtc<sup>1</sup>|N/A| Spécifie la durée de mise en file d’attente de l’événement dans le flux à partir duquel commencer le traitement. Lorsque `initialOffsetOptions/type` est configuré sur `fromEnqueuedTime`, ce paramètre est obligatoire. Prend en charge une valeur temporelle dans n’importe quel format pris en charge par [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime), par exemple `2020-10-26T20:31Z`. Par souci de clarté, vous devez également spécifier un fuseau horaire. Lorsque aucun fuseau horaire n’est spécifié, Functions adopte le fuseau horaire local de l’ordinateur exécutant l’application de fonction, qui est l’heure UTC en cas d’exécution sur Azure. Pour plus d’informations, consultez la [documentation sur EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|

<sup>1</sup> La prise en charge de `initialOffsetOptions` commence avec [EventHubs v4.2.0](https://github.com/Azure/azure-functions-eventhubs-extension/releases/tag/v4.2.0).

> [!NOTE]
> Pour obtenir des informations de référence sur le fichier host.json dans Azure Functions 2.x et ultérieur, consultez [Informations de référence sur le fichier host.json pour Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxBatchSize|64|Nombre d’événements maximal reçu par boucle de réception.|
|prefetchCount|n/a|Prérécupérations par défaut utilisées par le `EventProcessorHost` sous-jacent.| 
|batchCheckpointFrequency|1|Nombre de lots d’événements à traiter avant de créer un point de contrôle de curseur EventHub.| 

> [!NOTE]
> Pour obtenir des informations de référence sur le fichier host.json dans Azure Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).
