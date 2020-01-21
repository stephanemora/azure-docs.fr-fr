---
title: Exemples relatifs au service Stockage Azure avec Python | Microsoft Docs
description: Affichez, téléchargez et exécutez des exemples de code et des applications pour Azure Storage. Découvrez des exemples permettant de bien démarrer avec les objets blob, les files d’attente, les tables et les fichiers à l’aide des bibliothèques de client de stockage Python.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747986"
---
# <a name="azure-storage-samples-using-python"></a>Exemples relatifs au service Stockage Azure avec Python

Les tableaux suivants donnent une vue d’ensemble de notre dépôt d’exemples et des scénarios traités dans chacun d’eux. Cliquez sur les liens pour afficher l’exemple de code correspondant dans GitHub.

> [!NOTE]
> Ces exemples utilisent la bibliothèque Python v2.1 de Stockage Azure. Pour obtenir le code de la version v12, consultez les [exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples) du dépôt GitHub.

## <a name="blob-samples-v21"></a>Exemples d’objets blob (v2.1)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Append Blob | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Objet blob de blocs | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Chiffrement côté client | [Managing storage account keys in Azure Key Vault with Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Copie d'un objet blob | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Create Container | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Delete Blob | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Delete Container | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Métadonnées/propriétés/statistiques des objets blob | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| ACL/métadonnées/propriétés du conteneur | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Get Page Ranges | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| Objet blob/conteneur de bail | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| Objet blob/conteneur de liste | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Objet blob de pages | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Shared Access Signature in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Propriétés du service | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Snapshot Blob | [Getting Started with Azure Blob Service in Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples-v21"></a>Exemples de fichiers (v2.1)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer des partages/répertoires/fichiers | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Créer des partages/répertoires/fichiers | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Propriétés/métadonnées du répertoire | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Télécharger des fichiers | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Propriétés/métadonnées/mesures de fichier | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| Propriétés du service de fichiers | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Liste des répertoires et des fichiers | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Partages de listes | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Propriétés/métadonnées/statistiques de partage | [Getting Started with Azure Files Service in Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples-v21"></a>Exemples de files d’attente (v2.1)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Ajouter un message | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Chiffrement côté client | [Managing storage account keys in Azure Key Vault with Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Créer des files d’attente | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Supprimer le message/la file d’attente | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Lire furtivement un message | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| ACL/métadonnées/statistiques de la file d’attente | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Propriétés du service de File d’attente | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Mise à jour de message | [Getting Started with Azure Queue Service in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples-v21"></a>Exemples de tables (v2.1)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer une table | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Supprimer l’entité/la table | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Insertion/fusion/remplacement d’entité | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Query Entities | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Tables de requête | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| ACL/propriétés de la table | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Update Entity | [Getting Started with Azure Table Service in Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Bibliothèque Exemples de code Azure

Pour voir la bibliothèque complète des exemples, consultez la bibliothèque d’[exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=storage) pour trouver des exemples de stockage Azure que vous pouvez télécharger et exécuter localement. Les exemples de code fournis par la bibliothèque sont au format .zip. Vous pouvez également parcourir et cloner le dépôt GitHub pour chaque exemple.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Guides de prise en main

Consultez les guides suivants si vous recherchez des instructions sur l’installation et la prise en main des bibliothèques clientes de stockage Azure.

* [Getting Started with Azure Blob Service in Python](../blobs/storage-quickstart-blobs-python.md)
* [Getting Started with Azure Queue Service in Python](../queues/storage-python-how-to-use-queue-storage.md)
* [Getting Started with Azure Table Service in Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Getting Started with Azure Files Service in Python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les exemples pour d’autres langages :

* .NET : [Exemples relatifs au service Stockage Azure avec .NET](storage-samples-dotnet.md)
* Java : [Exemples relatifs au service Stockage Azure avec Java](storage-samples-java.md)
* JavaScript/Node.js : [Exemples relatifs au service Stockage Azure en JavaScript](storage-samples-javascript.md)
* Tous les autres langages : [Exemples relatifs à Stockage Azure](storage-samples.md)
