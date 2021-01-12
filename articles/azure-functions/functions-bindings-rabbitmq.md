---
title: Liaisons Azure RabbitMQ pour Azure Functions
description: Découvrez comment envoyer des déclencheurs et des liaisons Azure RabbitMQ dans Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746522"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Vue d’ensemble des liaisons RabbitMQ pour Azure Functions

> [!NOTE]
> Les liaisons RabbitMQ sont uniquement prises en charge de manière complète dans les plans **Premium et Dedicated**. La consommation n’est pas prise en charge.

La solution Azure Functions s’intègre avec [RabbitMQ](https://www.rabbitmq.com/) via des [déclencheurs et liaisons](./functions-triggers-bindings.md). L’extension RabbitMQ pour Azure Functions vous permet d’envoyer et de recevoir des messages à l’aide de l’API RabbitMQ avec Functions.

| Action | Type |
|---------|---------|
| Exécuter une fonction quand un message RabbitMQ transite par la file d’attente | [Déclencheur](./functions-bindings-rabbitmq-trigger.md) |
| Envoyer des messages RabbitMQ |[Liaison de sortie](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

Pour commencer à développer avec cette extension, veillez au préalable à [configurer un point de terminaison RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Pour en savoir plus sur RabbitMQ, consultez la [page de prise en main](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-3x-and-higher"></a>Functions 3.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 4.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Functions 1.x et 2.x

Les extensions de liaison RabbitMQ ne sont pas prises en charge pour Functions 1.x et 2.x. Utilisez Functions 3.x et les versions ultérieures.

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction lors de la création d’un message RabbitMQ (déclencheur)](./functions-bindings-rabbitmq-trigger.md)
- [Envoyer des messages RabbitMQ à partir d’Azure Functions (liaison de sortie)](./functions-bindings-rabbitmq-output.md)