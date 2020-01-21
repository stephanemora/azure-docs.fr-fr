---
title: Exemples de stockage Azure avec .NET| Microsoft Docs
description: Affichez, téléchargez et exécutez des exemples de code et des applications pour Azure Storage. Découvrez la mise en route des exemples d’objets blob, de files d’attente, de tables et de fichiers qui utilisent des bibliothèques clientes de stockage .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 50c5067c3db2f07da225b72d9ba0a8f0bdc44368
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748141"
---
# <a name="azure-storage-samples-using-net"></a>Exemples de stockage Azure avec .NET

Le tableau suivant fournit une vue d’ensemble de notre référentiel d’exemples et des scénarios traités dans chaque exemple. Cliquez sur les liens pour afficher l’exemple de code correspondant dans GitHub.

> [!NOTE]
> Ces exemples utilisent la bibliothèque .NET v11 du Stockage Azure. Pour obtenir le code de la version v12, consultez les [exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples) du dépôt GitHub.

## <a name="blob-samples-v11"></a>Exemples d’objets blob (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Append Blob | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Objet blob de blocs | [Application web de la galerie photos d’Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Chiffrement côté client | [Exemples de chiffrement d’objets blob](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Copie d'un objet blob | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Create Container | [Application web de la galerie photos d’Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete Blob | [Application web de la galerie photos d’Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete Container | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Métadonnées/propriétés/statistiques des objets blob | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| ACL/métadonnées/propriétés du conteneur | [Application web de la galerie photos d’Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Get Page Ranges | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Objet blob/conteneur de bail | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Objet blob/conteneur de liste | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Objet blob de pages | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Propriétés du service | [Prise en main des objets blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Snapshot Blob | [Sauvegarder des disques de machine virtuelle Azure avec des instantanés incrémentiels](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples-v11"></a>Exemples de fichiers (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer des partages/répertoires/fichiers | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Créer des partages/répertoires/fichiers | [Prise en main du service de fichiers Azure dans .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Propriétés/métadonnées du répertoire | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Télécharger des fichiers | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Propriétés/métadonnées/mesures de fichier | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Propriétés du service de fichiers | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Liste des répertoires et des fichiers | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Partages de listes | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Propriétés/métadonnées/statistiques de partage | [Exemple de stockage de fichier .NET Stockage Azure](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples-v11"></a>Exemples de files d’attente (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Ajouter un message | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Chiffrement côté client | [Chiffrement côté client de la file d’attente .NET Stockage Azure](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Créer des files d’attente | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Supprimer le message/la file d’attente | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Lire furtivement un message | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| ACL/métadonnées/statistiques de la file d’attente | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Propriétés du service de File d’attente | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Mise à jour de message | [Prise en main du service de File d’attente Azure dans .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples-v11"></a>Exemples de tables (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer une table | [Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Supprimer l’entité/la table | [Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Insertion/fusion/remplacement d’entité | [Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Query Entities | [Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tables de requête | [Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| ACL/propriétés de la table | [Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Update Entity | [Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Bibliothèque Exemples de code Azure

Pour voir la bibliothèque complète des exemples, consultez la bibliothèque d’[exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=storage) pour trouver des exemples de stockage Azure que vous pouvez télécharger et exécuter localement. Les exemples de code fournis par la bibliothèque sont au format .zip. Vous pouvez également parcourir et cloner le dépôt GitHub pour chaque exemple.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guides de prise en main

Consultez les guides suivants si vous recherchez des instructions sur l’installation et la prise en main des bibliothèques clientes de stockage Azure.

* [Prise en main du service BLOB Azure dans .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Prise en main du service de File d’attente Azure dans .NET](../storage-dotnet-how-to-use-queues.md)
* [Prise en main du service de Table Azure dans .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Prise en main du service de fichiers Azure dans .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les exemples pour d’autres langages :

* Java : [Exemples relatifs au service Stockage Azure avec Java](storage-samples-java.md)
* JavaScript/Node.js : [Exemples relatifs au service Stockage Azure en JavaScript](storage-samples-javascript.md)
* Python : [Exemples relatifs au service Stockage Azure avec Python](storage-samples-python.md)
* Tous les autres langages : [Exemples relatifs à Stockage Azure](../storage-samples.md)
