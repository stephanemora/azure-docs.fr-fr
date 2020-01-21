---
title: Exemples relatifs au service Stockage Azure en JavaScript | Microsoft Docs
description: Affichez, téléchargez et exécutez des exemples de code et des applications pour Azure Storage. Découvrez des exemples permettant de bien démarrer avec les objets blob, les files d’attente, les tables et les fichiers à l’aide des bibliothèques de clients de stockage JavaScript/Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 44fe68b8b04a1192c928e04c7d2a9d147f400130
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748295"
---
# <a name="azure-storage-samples-using-javascript"></a>Exemples relatifs au service Stockage Azure en JavaScript

Les tableaux suivants donnent une vue d’ensemble de notre dépôt d’exemples et des scénarios traités dans chacun d’eux. Cliquez sur les liens pour afficher l’exemple de code correspondant dans GitHub.

> [!NOTE]
> Ces exemples utilisent la bibliothèque JavaScript v10 de Stockage Azure. Pour obtenir le code de la version v12, consultez les [exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) du dépôt GitHub.

## <a name="blob-samples-v10"></a>Exemples d’objets blob (v10)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Objet blob de blocs | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| Chiffrement côté client | [Gestion des clés de compte de stockage dans Azure Key Vault avec JavaScript](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Copie d'un objet blob | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| Create Container | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| Delete Blob | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| Delete Container | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| Métadonnées d’objets blob | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| Propriétés d’objets blob | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| ACL de conteneur | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| Métadonnées de conteneur | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| Propriétés du conteneur | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| Get Page Ranges | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Lease Blob | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| Lease Container | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| Objet blob/conteneur de liste | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| Objet blob de pages | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [Signature d’accès partagé en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| Propriétés du service | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| Définir les règles CORS | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Snapshot Blob | [Bien démarrer avec le service Blob Azure en JavaScript](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples-v10"></a>Exemples de fichiers (v10)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Créer des partages/répertoires/fichiers | [Bien démarrer avec le service Fichier Azure en JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| Créer des partages/répertoires/fichiers | [Bien démarrer avec le service Fichier Azure en JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| Télécharger des fichiers | [Bien démarrer avec le service Fichier Azure en JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| Liste des répertoires et des fichiers | [Bien démarrer avec le service Fichier Azure en JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| Partages de listes | [Bien démarrer avec le service Fichier Azure en JavaScript](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples-v10"></a>Exemples de files d’attente (v10)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Ajouter un message | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| Chiffrement côté client | [Gestion des clés de compte de stockage dans Azure Key Vault avec JavaScript](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Créer des files d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| Supprimer un message | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| Supprimer une file d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| Lister les files d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| Lire furtivement un message | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| ACL de file d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| Règles CORS de file d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| Métadonnées de file d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| Propriétés du service de File d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| Statistiques de file d’attente | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| Mise à jour de message | [Bien démarrer avec le service File d’attente Azure en JavaScript](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples-v10"></a>Exemples de tables (v10)

| **Scénario** | **Exemple de Code** |
|--------------|-----------------|
| Traitement par lots sur les entités | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| Créer une table | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| Supprimer l’entité/la table | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| Insertion/fusion/remplacement d’entité | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| Lister des tables | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| Query Entities | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| Tables de requête | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| Requête de plage | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [Signature d’accès partagé en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| ACL de table | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| Règles CORS de table | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| Propriétés de la table | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| Statistiques de table | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| Update Entity | [Bien démarrer avec le service Table Azure en JavaScript](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Bibliothèque Exemples de code Azure

Pour voir la bibliothèque complète des exemples, consultez la bibliothèque d’[exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=storage) pour trouver des exemples de stockage Azure que vous pouvez télécharger et exécuter localement. Les exemples de code fournis par la bibliothèque sont au format .zip. Vous pouvez également parcourir et cloner le dépôt GitHub pour chaque exemple.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>Guides de prise en main

Consultez les guides suivants si vous recherchez des instructions sur l’installation et la prise en main des bibliothèques clientes de stockage Azure.

* [Bien démarrer avec le service Blob Azure en JavaScript](../blobs/storage-quickstart-blobs-nodejs.md)
* [Bien démarrer avec le service File d’attente Azure en JavaScript](../queues/storage-nodejs-how-to-use-queues.md)
* [Bien démarrer avec le service Table Azure en JavaScript](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les exemples pour d’autres langages :

* .NET : [Exemples relatifs au service Stockage Azure avec .NET](storage-samples-dotnet.md)
* Java : [Exemples relatifs au service Stockage Azure avec Java](storage-samples-java.md)
* Python : [Exemples relatifs au service Stockage Azure avec Python](storage-samples-python.md)
* Tous les autres langages : [Exemples relatifs à Stockage Azure](storage-samples.md)
