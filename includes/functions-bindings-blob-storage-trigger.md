---
title: Fichier Include
description: Fichier Include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 16ab569428510b3b423d6727fd31ee450a8d197e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381600"
---
### <a name="default"></a>Default

Vous pouvez utiliser les types de paramètres suivants pour l’objet blob déclencheur :

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Si vous essayez de lier à un des types de SDK Stockage et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

La liaison à `string` ou `Byte[]` est recommandée uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`. Pour plus d’informations, consultez [Concurrence et utilisation de la mémoire](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) plus loin dans cet article.

### <a name="additional-types"></a>Autres types

Les applications qui utilisent la [version 5.0.0 ou ultérieure de l’extension de stockage](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) peuvent également utiliser des types du [SDK Azure pour .NET](/dotnet/api/overview/azure/storage.blobs-readme). Cette version supprime la prise en charge des types hérités `ICloudBlob`, `CloudBlockBlob`, `CloudPageBlob` et `CloudAppendBlob` en faveur des types suivants :

- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Pour obtenir des exemples d’utilisation de ces types, consultez [le dépôt GitHub pour l’extension](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
