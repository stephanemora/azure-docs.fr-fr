---
title: Présentation du déclencheur et des liaisons de Stockage File d'attente Azure pour Azure Functions
description: Comprendre comment utiliser la liaison de sortie et le déclencheur Stockage File d’attente Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 252a3316ae14e17c2712547f088477d74da2c66e
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658675"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Présentation du déclencheur et des liaisons de Stockage File d'attente Azure pour Azure Functions

Azure Functions peut s’exécuter lorsque des messages de Stockage File d’attente Azure sont créés et écrire des messages de file d’attente dans une fonction.

| Action | Type |
|---------|---------|
| Exécuter une fonction pour modifier des données de stockage de file d’attente | [Déclencheur](./functions-bindings-storage-queue-trigger.md) |
| Écrire des messages de stockage de file d’attente |[Liaison de sortie](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

#### <a name="storage-extension-5x-and-higher"></a>Extension Stockage 5.x et ultérieur

Une nouvelle version de l’extension de liaisons de Stockage est disponible sous la forme d’un [package NuGet en préversion](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3). Cette préversion introduit la possibilité de [se connecter à l’aide d’une identité au lieu d’un secret](./functions-reference.md#configure-an-identity-based-connection). Pour les applications .NET, elle change également les types vers lesquels vous pouvez effectuer une liaison, en remplaçant les types de `WindowsAzure.Storage` et `Microsoft.Azure.Storage` par des types plus récents de [Azure.Storage.Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> Le package en préversion n’étant pas inclus dans un bundle d’extensions, il doit être installé manuellement. Pour les applications .NET, ajoutez une référence au package. Pour tous les autres types d’applications, consultez [Mettre à jour vos extensions].

[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Pour obtenir une référence de host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|maxPollingInterval|00:01:00|Intervalle maximal entre les interrogations de la file d’attente. L’intervalle minimal est 00:00:00.100 (100 ms). Les intervalles sont incrémentés jusqu’à `maxPollingInterval`. La valeur par défaut de `maxPollingInterval` est 00:01:00 (1 min). `maxPollingInterval` ne doit pas être inférieur à 00:00:00.100 (100 ms). Dans Functions 2.x et versions ultérieures, le type de données est `TimeSpan`. Dans Functions 1.x, il correspond aux millisecondes.|
|visibilityTimeout|00:00:00|Intervalle de temps entre les nouvelles tentatives en cas d’échec du traitement d’un message. |
|batchSize|16|Le nombre de messages de file d’attente que le runtime Functions récupère simultanément et traite en parallèle. Quand le nombre de messages en cours de traitement descend à `newBatchThreshold`, le runtime obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par fonction est `batchSize` plus `newBatchThreshold`. Cette limite s’applique séparément à chaque fonction déclenchée par une file d’attente. <br><br>Si vous souhaitez éviter les exécutions parallèles pour les messages reçus sur une file d’attente, vous pouvez définir `batchSize` sur 1. Toutefois, ce paramètre évite les opérations simultanées tant que votre application de fonction s’exécute uniquement sur une machine virtuelle unique. Si l’application de fonction augmente la taille des instances sur plusieurs machines virtuelles, chaque machine virtuelle peut exécuter une instance de chaque fonction déclenchée par une file d’attente.<br><br>La valeur `batchSize` maximale est de 32. |
|maxDequeueCount|5|Nombre de tentatives de traitement d’un message avant de le placer dans la file d’attente de messages incohérents.|
|newBatchThreshold|N*batchSize/2|Quand le nombre de messages traités simultanément passe en dessous de cette valeur, le runtime récupère un autre lot.<br><br>`N` représente le nombre de processeurs virtuels disponibles lors de l’exécution sur App Service ou les plans Premium. Sa valeur est `1` pour le plan Consommation.|
|messageEncoding|base64| Ce paramètre n’est disponible que dans les [versions 5.0.0 et supérieures de l’extension](#storage-extension-5x-and-higher). Il représente le format d’encodage des messages. Les valeurs valides sont `base64` et `none`.|

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction pour modifier des données de stockage de file d’attente (déclencheur)](./functions-bindings-storage-queue-trigger.md)
- [Écrire des messages de stockage de file d’attente (liaison de sortie)](./functions-bindings-storage-queue-output.md)
