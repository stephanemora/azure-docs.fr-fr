---
title: Architectures de référence de rendu Azure - Azure Batch
description: Architectures pour utiliser Azure Batch et d’autres services Azure afin d’étendre un groupe de rendus local en évoluant vers le cloud
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543493"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architectures de référence pour le rendu Azure

Cet article présente des diagrammes d’architecture de niveau supérieur pour les scénarios afin d’étendre, ou de faire évoluer, un groupe de rendus local vers Azure. Les exemples montrent différentes options pour les services de calcul, de mise en réseau et de stockage Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybride avec NFS ou CFS

Le diagramme suivant présente un scénario hybride qui inclut les services Azure suivants :

* **Calcul** : pool Azure Batch ou groupe de machines virtuelles identiques.

* **Réseau** - local : Azure ExpressRoute ou VPN. Azure : Azure VNet.

* **Stockage** - Fichiers d’entrée et de sortie : NFS ou CFS à l’aide de machines virtuelles Azure, synchronisées avec le stockage local via Azure File Sync ou RSync.

  ![« Cloud Bursting » - Hybride avec NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hybride avec Blobfuse

Le diagramme suivant présente un scénario hybride qui inclut les services Azure suivants :

* **Calcul** : pool Azure Batch ou groupe de machines virtuelles identiques.

* **Réseau** - local : Azure ExpressRoute ou VPN. Azure : Azure VNet.

* **Stockage** - Fichiers d’entrée et de sortie : stockage d’objets blob, monté pour calculer des ressources via Azure Blobfuse.

  ![« Cloud Bursting » - Hybride avec Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Calcul et stockage hybrides

Le diagramme suivant présente un scénario hybride entièrement connecté pour le calcul et le stockage, et inclut les services Azure suivants :

* **Calcul** : pool Azure Batch ou groupe de machines virtuelles identiques.

* **Réseau** - local : Azure ExpressRoute ou VPN. Azure : Azure VNet.

* **Stockage** : entre locaux : Avere vFXT. Archivage facultatif de fichiers locaux via Azure Data Box vers le stockage d’objets blob.

  ![« Cloud Bursting » - calcul et stockage hybrides](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’utilisation de [gestionnaires de rendus](batch-rendering-render-managers.md) avec Azure Batch.

* En savoir plus sur les options de [rendu dans Azure](batch-rendering-service.md).