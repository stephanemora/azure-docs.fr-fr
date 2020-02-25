---
title: Présentation du déclencheur et des liaisons de Stockage File d'attente Azure pour Azure Functions
description: Comprendre comment utiliser la liaison de sortie et le déclencheur Stockage File d’attente Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 16c325f2d6ea78ba980406f56839dd4d0b64bee1
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482256"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Présentation du déclencheur et des liaisons de Stockage File d'attente Azure pour Azure Functions

Azure Functions peut s’exécuter lorsque des messages de Stockage File d’attente Azure sont créés et écrire des messages de file d’attente dans une fonction.

| Action | Type |
|---------|---------|
| Exécuter une fonction pour modifier des données de stockage de file d’attente | [Déclencheur](./functions-bindings-storage-queue-trigger.md) |
| Écrire des messages de stockage de file d’attente |[Liaison de sortie](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, consultez [Mettre à jour vos extensions]. |

[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Mettre à jour vos extensions]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction pour modifier des données de stockage de file d’attente (déclencheur)](./functions-bindings-storage-queue-trigger.md)
- [Écrire des messages de stockage de file d’attente (liaison de sortie)](./functions-bindings-storage-blob-output.md)