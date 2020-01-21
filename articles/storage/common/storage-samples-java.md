---
title: Exemples de stockage Azure avec Java| Microsoft Docs
description: Affichez, téléchargez et exécutez des exemples de code et des applications pour Azure Storage. Découvrez des exemples de mise en route d’objets blob, de files d’attente, de tables et de fichiers à l’aide des bibliothèques clientes de stockage Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: dfdde1ae981dcd2d539dec3667e44e90cef4d1c8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748032"
---
# <a name="azure-storage-samples-using-java"></a>Exemples de stockage Azure avec Java

Le tableau suivant fournit une vue d’ensemble de notre référentiel d’exemples et des scénarios traités dans chaque exemple. Cliquez sur les liens pour afficher l’exemple de code correspondant dans GitHub.

> [!NOTE]
> Ces exemples utilisent la bibliothèque Java v11 de Stockage Azure. Pour obtenir le code de la version v12, consultez les [exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) du dépôt GitHub.

## <a name="blob-samples-v11"></a>Exemples d’objets blob (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Append Blob | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Objet blob de blocs | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Chiffrement côté client | [Getting Started with Azure Client Side Encryption in Java (Prise en main du chiffrement côté client Azure dans Java)](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| Copie d'un objet blob | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Create Container | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Delete Blob | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Delete Container | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Métadonnées/propriétés/statistiques des objets blob | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| ACL/métadonnées/propriétés du conteneur | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Get Page Ranges | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| Objet blob/conteneur de bail | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Objet blob/conteneur de liste | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Objet blob de pages | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [Exemple de tests SAS](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| Propriétés du service | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Snapshot Blob | [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples-v11"></a>Exemples de fichiers (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer des partages/répertoires/fichiers | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Créer des partages/répertoires/fichiers | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Propriétés/métadonnées du répertoire | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Télécharger des fichiers | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Propriétés/métadonnées/mesures de fichier | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Propriétés du service de fichiers | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Liste des répertoires et des fichiers | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Partages de listes | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Propriétés/métadonnées/statistiques de partage | [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples-v11"></a>Exemples de files d’attente (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Ajouter un message | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| Chiffrement côté client | [Getting Started with Azure Client Side Encryption in Java (Prise en main du chiffrement côté client Azure dans Java)](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| Créer des files d’attente | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Supprimer le message/la file d’attente | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Lire furtivement un message | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| ACL/métadonnées/statistiques de la file d’attente | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Propriétés du service de File d’attente | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Mise à jour de message | [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples-v11"></a>Exemples de tables (v11)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer une table | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Supprimer l’entité/la table | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Insertion/fusion/remplacement d’entité | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Query Entities | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Tables de requête | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| ACL/propriétés de la table | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| Update Entity | [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Bibliothèque Exemples de code Azure

Pour voir la bibliothèque complète des exemples, consultez la bibliothèque d’[exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=storage) pour trouver des exemples de stockage Azure que vous pouvez télécharger et exécuter localement. Les exemples de code fournis par la bibliothèque sont au format .zip. Vous pouvez également parcourir et cloner le dépôt GitHub pour chaque exemple.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Guides de prise en main

Consultez les guides suivants si vous recherchez des instructions sur l’installation et la prise en main des bibliothèques clientes de stockage Azure.

* [Getting Started with Azure Blob Service in Java (Prise en main du service Azure Blob en Java)](../blobs/storage-quickstart-blobs-java.md)
* [Getting Started with Azure Queue Service in Java (Prise en main du service de File d’attente Azure en Java)](../queues/storage-java-how-to-use-queue-storage.md)
* [Getting Started with Azure Table Service in Java (Prise en main du service de Table Azure en Java)](../../cosmos-db/table-storage-how-to-use-java.md)
* [Getting Started with Azure File Service in Java (Prise en main du service de fichiers Azure en Java)](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les exemples pour d’autres langages :

* .NET : [Exemples relatifs au service Stockage Azure avec .NET](storage-samples-dotnet.md)
* JavaScript/Node.js : [Exemples relatifs au service Stockage Azure en JavaScript](storage-samples-javascript.md)
* Python : [Exemples relatifs au service Stockage Azure avec Python](storage-samples-python.md)
* Tous les autres langages : [Exemples relatifs à Stockage Azure](storage-samples.md)
