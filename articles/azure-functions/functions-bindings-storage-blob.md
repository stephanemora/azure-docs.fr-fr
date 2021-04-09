---
title: Déclencheur et liaisons de Stockage Blob Azure pour Azure Functions
description: Découvrez comment utiliser le déclencheur et les liaisons Stockage Blob Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381526"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Vue d’ensemble des liaisons de Stockage Blob Azure pour Azure Functions

Azure Functions s’intègre à [Stockage Azure](../storage/index.yml) via [des déclencheurs et des liaisons](./functions-triggers-bindings.md). L’intégration à Stockage Blob vous permet de créer des fonctions qui réagissent aux modifications de données d’objets Blob ainsi qu’à la lecture et à l’écriture de valeurs.

| Action | Type |
|---------|---------|
| Exécuter une fonction quand des données de stockage d’objets Blob sont modifiées | [Déclencheur](./functions-bindings-storage-blob-trigger.md) |
| Lire des données de stockage d’objets Blob dans une fonction | [Liaison d’entrée](./functions-bindings-storage-blob-input.md) |
| Autoriser une fonction à écrire des données de stockage d’objets Blob |[Liaison de sortie](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

#### <a name="storage-extension-5x-and-higher"></a>Extension Stockage 5.x et ultérieur

Une nouvelle version de l’extension de liaisons de Stockage est disponible sous la forme d’un [package NuGet en préversion](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Cette préversion introduit la possibilité de [se connecter à l’aide d’une identité au lieu d’un secret](./functions-reference.md#configure-an-identity-based-connection). Pour les applications .NET, elle change également les types vers lesquels vous pouvez effectuer une liaison, en remplaçant les types de `WindowsAzure.Storage` et `Microsoft.Azure.Storage` par des types plus récents de [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs).

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

## <a name="hostjson-settings"></a>Paramètres host.json

> [!NOTE]
> Cette section ne s’applique pas lors de l’utilisation de versions d’extension antérieures à 5.0.0. Pour ces versions, il n’existe aucun paramètre de configuration global pour les blobs.

Cette section décrit les paramètres de configuration globaux disponibles pour cette liaison lors de l’utilisation des [extensions versions 5.0.0 et ultérieures](#storage-extension-5x-and-higher). L’exemple de fichier *host.json* ci-dessous contient seulement les paramètres des versions 2.x et ultérieures pour cette liaison. Pour plus d’informations sur les paramètres de configuration globaux dans les versions de Functions 2.x et ultérieures, consultez [Informations de référence sur le fichier host.json pour Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (nombre de mémoires à tores magnétiques)|Nombre entier d’appels simultanés autorisés pour chaque fonction déclenchée par un objet blob. La valeur minimale autorisée est de 1.|

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction quand les données de stockage Blob changent](./functions-bindings-storage-blob-trigger.md)
- [Lire des données de stockage de blobs lors de l’exécution d’une fonction](./functions-bindings-storage-blob-input.md)
- [Écrire des données de stockage d’objets blob à partir d’une fonction](./functions-bindings-storage-blob-output.md)
