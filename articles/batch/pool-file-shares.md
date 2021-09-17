---
title: Partage de fichiers Azure pour des pools Azure Batch
description: Guide pratique pour monter un partage Azure Files à partir de nœuds de calcul dans un pool Linux ou Windows dans Azure Batch.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 2c5cbf1a3e5d74927ddc74c4838c31f68a348876
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772113"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Utiliser un partage de fichiers Azure avec un pool Batch

[Azure Files](../storage/files/storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles par le biais du protocole SMB (Server Message Block). Vous pouvez monter et utiliser un partage de fichiers Azure sur des nœuds de calcul de pool Batch.

## <a name="considerations-for-use-with-batch"></a>Considérations relatives à l’utilisation de Batch

Si vous utilisez Azure Files non Premium, envisagez d’utiliser un partage de fichiers Azure quand vous avez des pools qui exécutent un nombre relativement faible de tâches en parallèle. Passez en revue les [objectifs de performance et d’extensibilité](../storage/files/storage-files-scale-targets.md) pour déterminer si Azure Files (qui utilise un compte Stockage Azure) doit être utilisé étant donné la taille attendue de votre pool et le nombre de fichiers d’actifs.

Les partages de fichiers Azure sont [rentables](https://azure.microsoft.com/pricing/details/storage/files/) et peuvent être configurés avec la réplication des données d’une autre région pour être redondants à l’échelle mondiale .

Vous pouvez monter un partage de fichiers Azure simultanément à partir d’un ordinateur local. Toutefois, veillez à bien comprendre les [implications de la concurrence](../storage/blobs/concurrency-manage.md), en particulier lors de l’utilisation des API REST.

Consultez également les [considérations de planification](../storage/files/storage-files-planning.md) générales pour les partages de fichiers Azure.

## <a name="create-a-file-share"></a>Créer un partage de fichiers

Vous pouvez créer un partage de fichiers Azure dans un compte de stockage lié à votre compte Batch, ou dans un compte de stockage distinct. Pour plus d’informations, consultez [Créer un partage de fichiers Azure](../storage/files/storage-how-to-create-file-share.md).

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Monter un partage de fichiers Azure sur un pool Batch

Pour plus d’informations sur le montage d’un partage de fichiers Azure sur un pool, consultez [Monter un système de fichiers virtuel sur un pool Batch](virtual-file-mount.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour d’autres options de lecture et d’écriture de données dans Batch, consultez [Conserver les résultats des tâches et des travaux](batch-task-output.md).
- Consultez également la boîte à outils [Batch Shipyard](https://github.com/Azure/batch-shipyard), qui contient des [recettes Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) afin de déployer des systèmes de fichiers pour des charges de travail de conteneur Batch.
