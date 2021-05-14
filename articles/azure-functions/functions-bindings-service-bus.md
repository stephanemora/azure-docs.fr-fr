---
title: Liaisons Azure Service Bus pour Azure Functions
description: Apprenez à envoyer des déclencheurs et des liaisons Azure Service Bus dans Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 4573e471b69c11b055d8f0f1dfd6416c8d158c20
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227917"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Liaisons Azure Service Bus pour Azure Functions

Azure Functions s’intègre avec [Azure Service Bus](https://azure.microsoft.com/services/service-bus) via [des déclencheurs et des liaisons](./functions-triggers-bindings.md). L’intégration avec Service Bus vous permet de créer des fonctions qui réagissent et envoient des messages de file d’attente ou de rubrique.

| Action | Type |
|---------|---------|
| Exécuter une fonction durant la création d’un message de file d’attente ou de rubrique Service Bus | [Déclencheur](./functions-bindings-service-bus-trigger.md) |
| Envoyer des messages Azure Service Bus |[Liaison de sortie](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

> [!NOTE]
> Actuellement, la liaison Service Bus ne prend pas en charge l’authentification à l’aide d’une identité managée. Utilisez plutôt une [signature d’accès partagé Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature).

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 4.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="service-bus-extension-5x-and-higher"></a>Extension Service Bus 5.x et versions ultérieures

Une nouvelle version de l’extension de liaisons Service Bus est disponible sous la forme d’un [package NuGet en préversion](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.2). Cette préversion introduit la possibilité de [se connecter à l’aide d’une identité au lieu d’un secret](./functions-reference.md#configure-an-identity-based-connection). Pour les applications .NET, elle change également les types avec lesquels vous pouvez établir une liaison, en remplaçant les types de `Microsoft.ServiceBus.Messaging` et `Microsoft.Azure.ServiceBus` par des types plus récents de [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus).

> [!NOTE]
> Le package en préversion n’étant pas inclus dans un bundle d’extensions, il doit être installé manuellement. Pour les applications .NET, ajoutez une référence au package. Pour tous les autres types d’applications, consultez [Mettre à jour vos extensions].

[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.


<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globaux disponibles pour cette liaison dans les versions 2.x et ultérieures. L’exemple de fichier host.json ci-dessous contient seulement les paramètres pour cette liaison. Pour plus d’informations sur les paramètres de configuration globale, consultez [Informations de référence sur le fichier host.json pour la version d’Azure Functions](functions-host-json.md).

> [!NOTE]
> Pour obtenir une référence de host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

Si vous avez défini `isSessionsEnabled` sur `true`, les options `sessionHandlerOptions` sont respectées.  Si vous avez défini `isSessionsEnabled` sur `false`, les options `messageHandlerOptions` sont respectées.

|Propriété  |Default | Description |
|---------|---------|---------|
|prefetchCount|0|Obtient ou définit le nombre de messages que le destinataire des messages peut demander simultanément.|
|maxAutoRenewDuration|00:05:00|Durée maximale pendant laquelle le verrouillage de message doit être renouvelé automatiquement.|
|autoComplete|true|Indique si le déclencheur doit terminer l’appel automatiquement après le traitement, ou si le code de la fonction termine manuellement l’appel.<br><br>La définition de `false` est prise en charge uniquement dans C# .<br><br>Si la valeur est `true`, le déclencheur termine automatiquement le message si l’exécution de la fonction se termine correctement et abandonne le message dans le cas contraire.<br><br>Lorsque la valeur est `false`, il vous incombe d’appeler des méthodes [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) pour terminer, abandonner ou mettre au rebut le message. Si une exception est levée (et qu’aucune des méthodes `MessageReceiver` n’est appelée), le verrou reste. Une fois le verrou expiré, le message est de nouveau mis en file d’attente avec `DeliveryCount` incrémenté, et le verrou est automatiquement renouvelé.<br><br>Dans les fonctions non C#, les exceptions de la fonction entraînent l’appel par le runtime de `abandonAsync` en arrière-plan. Si aucune exception ne se produit, `completeAsync` est appelé en arrière-plan. |
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier par instance mise à l’échelle. Par défaut, le runtime Functions traite plusieurs messages simultanément.|
|maxConcurrentSessions|2000|Nombre maximal de sessions qui peuvent être traitées simultanément par instance mise à l’échelle.|

### <a name="additional-settings-for-version-5x"></a>Paramètres supplémentaires pour la version 5.x et ultérieure

L’exemple de fichier host.json ci-dessous contient uniquement les paramètres pour les versions 5.0.0 et ultérieures de l’extension Service Bus.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "serviceBusOptions": {
                "retryOptions":{
                    "mode": "exponential",
                    "tryTimeout": "00:00:10",
                    "delay": "00:00:00.80",
                    "maxDelay": "00:01:00",
                    "maxRetries": 4
                },
                "prefetchCount": 100,
                "autoCompleteMessages": true,
                "maxAutoLockRenewalDuration": "00:05:00",
                "maxConcurrentCalls": 32,
                "maxConcurrentSessions": 10,
                "maxMessages": 2000,
                "sessionIdleTimeout": "00:01:00",
                "maxAutoLockRenewalDuration": "00:05:00"
            }
        }
    }
}
```

Lorsque vous utilisez les versions 5.x et ultérieures de l’extension Service Bus, les paramètres de configuration globale suivants sont pris en charge en plus des paramètres 2.x dans `ServiceBusOptions`.

|Propriété  |Default | Description |
|---------|---------|---------|
|prefetchCount|0|Obtient ou définit le nombre de messages que le destinataire des messages peut demander simultanément.|
|autoCompleteMessages|true|Détermine s’il faut ou non terminer automatiquement les messages après l’exécution réussie de la fonction et doit être utilisé à la place du paramètre de configuration `autoComplete`.|
|maxAutoLockRenewalDuration|00:05:00|À utiliser à la place de `maxAutoRenewDuration`.|
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier par instance mise à l’échelle. Par défaut, le runtime Functions traite plusieurs messages simultanément.|
|maxConcurrentSessions|8|Nombre maximal de sessions qui peuvent être traitées simultanément par instance mise à l’échelle.|
|maxMessages|1 000|Nombre maximal de messages qui seront transmis à chaque appel de fonction. Cela s’applique uniquement aux fonctions qui reçoivent un lot de messages.|
|sessionIdleTimeout|n/a|Délai d’attente maximal pour la réception d’un message pour la session active. Une fois ce délai écoulé, l’UC ferme la session et tente de traiter une autre session.|

### <a name="retry-settings"></a>Paramètres de nouvelle tentative

Outre les propriétés de configuration ci-dessus, lorsque vous utilisez les versions 5.x et ultérieures de l’extension Service Bus, vous pouvez également configurer `RetryOptions` à partir de `ServiceBusOptions`. Ces paramètres déterminent si une opération qui a échoué doit faire l’objet d’une nouvelle tentative et, le cas échéant, la durée d’attente entre chaque tentative. Les options contrôlent également la durée autorisée pour la réception des messages et d’autres interactions avec le service Service Bus.

|Propriété  |Default | Description |
|---------|---------|---------|
|mode|Exponentielle|Approche à utiliser pour calculer les délais de nouvelle tentative. Le mode exponentiel par défaut effectue de nouvelles tentatives avec un délai basé sur une stratégie de backoff, où chaque tentative augmente la durée d’attente avant la nouvelle tentative. Le mode `Fixed` effectue de nouvelles tentatives à intervalles fixes, chaque délai ayant la même durée.|
|tryTimeout|00:00:10|Durée maximale d’attente d’une opération par tentative.|
|delay|00:00:00.80|Délai ou facteur de backoff à appliquer entre les nouvelles tentatives.|
|maxDelay|00:01:00|Délai maximal à accorder entre les nouvelles tentatives.|
|maxRetries|3|Nombre maximal de nouvelles tentatives avant de considérer que l’opération associée a échoué.|

---

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction durant la création d’un message de file d’attente ou de rubrique Service Bus (déclencheur)](./functions-bindings-service-bus-trigger.md)
- [Envoyer des messages Azure Service Bus à partir d’Azure Functions (liaison de sortie)](./functions-bindings-service-bus-output.md)