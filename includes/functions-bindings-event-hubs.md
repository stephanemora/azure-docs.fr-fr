---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5a3b9ab410ff8a757aa3ef23ee7d097ee1b3071b
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077930"
---
## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[Bundle d’extensions]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Mettre à jour vos extensions]: ../articles/azure-functions/functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Extension Event Hubs 5.x et versions ultérieures

Une nouvelle version de l’extension de liaisons Event Hubs est disponible en préversion. Elle introduit la possibilité de [se connecter à l’aide d’une identité plutôt que d’un secret](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). Pour les applications .NET, elle change également les types vers lesquels vous pouvez établir une liaison, en remplaçant les types de `Microsoft.Azure.EventHubs` par des types plus récents de [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs).

Cette version de l’extension est disponible sous la forme d’un [package NuGet en préversion] ou peut être ajoutée à partir de la version 3 du bundle d’extensions en préversion en ajoutant le code suivant dans votre fichier `host.json` :

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Preview",
    "version": "[3.*, 4.0.0)"
  }
}
```

Pour en savoir plus, consultez [Mettre à jour vos extensions].

[Package NuGet en préversion]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.7
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.

## <a name="hostjson-settings"></a>Paramètres host.json
<a name="host-json"></a>

Le fichier [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contient les paramètres qui contrôlent le comportement du déclencheur Event Hubs. La configuration diffère selon la version d’Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]