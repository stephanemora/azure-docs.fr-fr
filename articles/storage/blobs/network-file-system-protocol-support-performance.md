---
title: Considérations relatives au niveau de performance du système NFS 3.0 dans Stockage Blob Azure | Microsoft Docs
description: Optimisez le niveau de performance de vos demandes de stockage du 	système de gestion de fichiers en réseau (NFS, Network File System) 3.0 en suivant les recommandations de cet article.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 1268df1aaf095fd6a965b447d48a9ef4978325d7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046883"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage"></a>Considérations relatives au niveau de performance de NFS (Network File System) 3.0 dans Stockage Blob Azure

Le stockage Blob Azure prend désormais en charge le protocole NFS (Network File System) 3.0. Cet article contient des recommandations à appliquer pour optimiser le niveau de performance des demandes de stockage. Pour plus d’informations sur la prise en charge du protocole NFS 3.0 dans Stockage Blob Azure, consultez [Prise en charge du protocole NFS (Network File System) 3.0 dans Stockage Blob Azure](network-file-system-protocol-support.md).

## <a name="add-clients-to-increase-throughput"></a>Ajout de clients pour augmenter le débit

Le Stockage Blob Azure évolue de façon linéaire jusqu’à la limite maximale de sortie et d’entrée du compte de stockage. Par conséquent, vos applications peuvent atteindre un débit plus élevé en utilisant plus de clients. Pour voir les limites d’entrée et de sortie des comptes de stockage, consultez [Scalabilité et objectifs de niveau de performance des comptes de Stockage Standard](../common/scalability-targets-standard-account.md).

Le graphe suivant montre comment la bande passante augmente lorsque d’autres clients sont ajoutés. Dans ce graphique, un client est une machine virtuelle (VM) et avec un compte de stockage v2 standard à usage général.

> [!div class="mx-imgBorder"]
> ![Niveau de performance Standard](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Le graphique suivant montre le même effet lorsqu’il est appliqué à un compte de stockage d’objets blob de blocs Premium.

> [!div class="mx-imgBorder"]
> ![Performances Premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-block-blob-storage-accounts-for-small-scale-applications"></a>Utiliser des comptes de stockage d’objets blob de blocs Premium pour des applications à petite échelle

Il n’est pas possible pour toutes les applications d’effectuer un scale-up en ajoutant des clients. Dans ce cas, le [compte de stockage d’objets blob de blocs Azure Premium](../common/storage-account-create.md) offre une faible latence et des taux de transaction élevés. Il peut atteindre une bande passante maximale avec moins de threads et de clients. Par exemple, avec un client unique, il peut enregistrer **2,3 fois** plus de bande passante que la même configuration avec un compte de stockage universel v2 à niveau de performance standard.

Chaque barre du graphe ci-dessous montre la différence entre la bande passante obtenue pour les comptes de stockage de niveau de performance standard et Premium. Plus le nombre de clients augmente, plus cette différence diminue.

> [!div class="mx-imgBorder"]
> ![Niveau de performance relatif](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="improve-read-ahead-size-to-increase-large-file-read-throughput"></a>Améliorer la taille de lecture anticipée pour augmenter le débit de lecture de fichiers volumineux

Le paramètre noyau read_ahead_kb représente la quantité de données supplémentaires qui doivent être lues après avoir satisfait à une demande de lecture donnée. Vous pouvez augmenter ce paramètre à 16 Mo pour améliorer le débit de lecture des fichiers volumineux.

```
export AZMNT=/your/container/mountpoint

echo 15728640 > /sys/class/bdi/0:$(stat -c "%d" $AZMNT)/read_ahead_kb
```

## <a name="avoid-frequent-overwrites-on-data"></a>Éviter les remplacements fréquents sur les données

Il faut plus de temps pour effectuer une opération de remplacement qu’une nouvelle opération d’écriture. En effet, une opération de remplacement NFS, en particulier une modification de fichier partielle sur place, est une combinaison de plusieurs opérations Blob sous-jacentes : une opération de lecture, une opération de modification et une opération d’écriture. Par conséquent, une application qui exige des modifications fréquentes sur place n’est pas adaptée aux comptes de Stockage Blob avec NFS.

## <a name="deploy-azure-hpc-cache-for-latency-sensitive-applications"></a>Déployer Azure HPC Cache pour les applications sensibles à la latence

Certaines applications peuvent nécessiter une faible latence en plus d’un débit élevé. Vous pouvez déployer [Azure HPC Cache](../../hpc-cache/nfs-blob-considerations.md) pour améliorer considérablement la latence. En savoir plus sur la [latence dans le stockage Blob](storage-blobs-latency.md).

## <a name="other-best-practice-recommendations"></a>Autres recommandations

- Utilisez des machines virtuelles dotées d’une bande passante réseau suffisante.

- Utilisez plusieurs points de montage lorsque vos charges de travail l’autorisent.

- Utilisez autant de threads que possible.

- Utilisez de grandes tailles de bloc.

- Effectuez des demandes de stockage à partir d’un client situé dans la même région que le compte de stockage, de façon à améliorer potentiellement le temps de réponse du réseau.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la prise en charge du protocole NFS 3.0 dans Stockage Blob Azure, consultez [Prise en charge du protocole NFS (Network File System) 3.0 dans Stockage Blob Azure](network-file-system-protocol-support.md).

- Pour démarrer, consultez [Monter le stockage Blob à l’aide du protocole NFS (Network File System) 3.0](network-file-system-protocol-support-how-to.md).
