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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381650"
---
### <a name="default"></a>Default

Vous pouvez lier aux types suivants pour écrire des blobs :

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> requiert la liaison « in » `direction` dans *function.json* ou `FileAccess.Read` dans une bibliothèque de classes C#. Toutefois, vous pouvez utiliser l’objet conteneur fourni par le runtime pour les opérations d’écriture, telles que le chargement de blobs dans le conteneur.

<sup>2</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Si vous essayez de lier à un des types de SDK Stockage et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

La liaison à `string` ou `Byte[]` est recommandée uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`. Pour plus d’informations, consultez [Concurrence et utilisation de la mémoire](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) plus haut dans cet article.

### <a name="additional-types"></a>Autres types

Les applications qui utilisent la [version 5.0.0 ou ultérieure de l’extension de stockage](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) peuvent également utiliser des types du [SDK Azure pour .NET](/dotnet/api/overview/azure/storage.blobs-readme). Cette version ne prend plus en charge les types hérités `CloudBlobContainer`, `CloudBlobDirectory`, `ICloudBlob`, `CloudBlockBlob`, `CloudPageBlob` et `CloudAppendBlob`, mais elle prend désormais en charge les types suivants :

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> requiert la liaison « in » `direction` dans *function.json* ou `FileAccess.Read` dans une bibliothèque de classes C#. Toutefois, vous pouvez utiliser l’objet conteneur fourni par le runtime pour les opérations d’écriture, telles que le chargement de blobs dans le conteneur.

<sup>2</sup> requiert la liaison « inout » `direction` dans *function.json* ou `FileAccess.ReadWrite` dans une bibliothèque de classes C#.

Pour obtenir des exemples d’utilisation de ces types, consultez [le dépôt GitHub pour l’extension](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
