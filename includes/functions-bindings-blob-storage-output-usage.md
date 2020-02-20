---
title: Fichier include
description: Fichier include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201966"
---
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

Dans les fonctions asynchrones, utilisez la valeur de retour ou `IAsyncCollector` au lieu d’un paramètre `out`.

La liaison à `string` ou `Byte[]` est recommandée uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`. Pour plus d’informations, consultez [Concurrence et utilisation de la mémoire](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) plus haut dans cet article.
