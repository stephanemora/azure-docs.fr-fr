---
title: Présentation d’Azure File Sync | Microsoft Docs
description: Vue d’ensemble d’Azure File Sync, un service qui vous permet de créer et d’utiliser les partages de fichiers réseau dans le cloud à l’aide du protocole SMB de normes industrielles.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107795840"
---
# <a name="what-is-azure-file-sync"></a>Présentation d’Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d'un serveur de fichiers Windows. Même si certains utilisateurs peuvent choisir de conserver une copie complète de leurs données localement, Azure File Sync permet également de transformer Windows Server en un cache rapide pour votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.   

## <a name="videos"></a>Videos
| Présentation d’Azure File Sync | Azure Files et Sync (Ignite 2019)  |
|-|-|
| [![Capture vidéo de la présentation d’Azure File Sync – Cliquez pour lancer la vidéo !](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Capture vidéo de la présentation d’Azure Files et Sync – Cliquez pour lancer la vidéo !](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Avantages d’Azure File Sync

### <a name="cloud-tiering"></a>Hiérarchisation cloud
Lorsque la hiérarchisation cloud est activée, les fichiers qui sont les plus fréquemment utilisés sont mis en cache sur votre serveur local, et les fichiers qui sont les moins fréquemment utilisés sont hiérarchisés dans le cloud. Vous pouvez contrôler la quantité d’espace disque local qui est utilisée pour la mise en cache. Les fichiers hiérarchisés peuvent être rapidement rappelés à la demande, ce qui fournit une expérience très fluide tout en permettant de réduire les coûts, car vous n’avez besoin de stocker qu’une fraction de vos données localement. Pour plus d’informations sur la hiérarchisation cloud, consultez [Vue d’ensemble de la hiérarchisation cloud](file-sync-cloud-tiering-overview.md). 

### <a name="multi-site-access-and-sync"></a>Accès et synchronisation multisites
Azure File Sync est idéal pour les scénarios d’accès distribué. Pour chacun de vos bureaux, vous pouvez provisionner un serveur Windows local dans le cadre de votre déploiement Azure File Sync. Les modifications apportées à un serveur dans un bureau sont automatiquement synchronisées avec les serveurs de tous les autres bureaux.

### <a name="business-continuity-and-disaster-recovery"></a>Continuité d’activité et reprise d’activité
Azure File Sync est associé à Azure Files, qui offre plusieurs options de redondance pour un stockage à haut niveau de disponibilité. Étant donné qu’Azure contient des copies résilientes de vos données, votre serveur local devient un appareil de mise en cache supprimable. En outre, vous pouvez effectuer la récupération à partir d’un serveur défaillant en ajoutant un nouveau serveur à votre déploiement Azure File Sync. Au lieu d’effectuer une restauration à partir d’une sauvegarde locale, vous provisionnez un autre serveur Windows Server, vous installez l’agent Azure File Sync sur celui-ci, puis vous l’ajoutez à votre déploiement Azure File Sync. Azure File Sync télécharge l’espace de noms de fichier avant de télécharger des données pour que votre serveur soit opérationnel le plus rapidement possible. Pour une récupération encore plus rapide, vous pouvez utiliser un serveur de secours actif dans le cadre de votre déploiement, ou vous pouvez utiliser Azure File Sync avec le clustering Windows.

## <a name="cloud-side-backup"></a>Sauvegarde côté cloud
Réduisez vos dépenses en sauvegarde locale en effectuant des sauvegardes centralisées dans le cloud à l’aide de la Sauvegarde Azure. Les partages SMB Azure Files comprennent des fonctionnalités d’instantané natives, et le processus peut être automatisé à l’aide de la Sauvegarde Azure pour planifier vos sauvegardes et gérer leur conservation. La Sauvegarde Azure s’intègre également à vos serveurs locaux. Par conséquent, lorsque vous effectuez une restauration dans le cloud, ces modifications sont automatiquement téléchargées sur vos serveurs Windows Server.  

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement d’Azure File Sync](file-sync-planning.md)
* [Vue d’ensemble de la hiérarchisation cloud](file-sync-cloud-tiering-overview.md)
* [Superviser Azure File Sync](file-sync-monitoring.md)