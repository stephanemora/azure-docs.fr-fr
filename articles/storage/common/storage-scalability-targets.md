---
title: Objectifs de performance et d’extensibilité du service Stockage Azure - Comptes de stockage
description: Obtenez plus d’informations sur les objectifs d’extensibilité et de performance, y compris la capacité, le taux de demandes et la bande passante entrante et sortante pour les comptes de stockage Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 293d0de024ad815b4755d48833b7058c29ebcd5a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513307"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Objectifs de performance et d’extensibilité du service Stockage Azure pour les comptes de stockage

Cet article présente les objectifs de performance et d’évolutivité pour les comptes de stockage Azure. Les objectifs d’extensibilité et de performances répertoriés ici sont des objectifs haut de gamme mais réalisables. Dans tous les cas, le taux de demande et la bande passante atteints par votre compte de stockage dépendent de la taille des objets stockés, des modèles d’accès utilisés et du type de charge de travail de votre application.

Veillez à tester votre service afin de déterminer si ses performances répondent à vos besoins. Dans la mesure du possible, évitez les pics soudains de trafic et assurez-vous que le trafic est bien réparti sur toutes les partitions.

Lorsque votre application atteint la limite de gestion d’une partition concernant la charge de travail, Stockage Azure commence à renvoyer des codes d’erreur 503 (Serveur occupé) ou 500 (Délai d’expiration de l’opération). Si vous rencontrez des erreurs 503, nous vous recommandons de modifier votre application pour utiliser une stratégie d’interruption exponentielle pour les nouvelles tentatives. L’interruption exponentielle diminue la charge sur la partition et atténue les pics de trafic pour cette partition.

## <a name="storage-account-scale-limits"></a>Limites d’évolutivité d’un compte de stockage

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limites d’évolutivité d’un compte de stockage Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limites d’évolutivité d’un fournisseur de ressources de stockage

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Blob Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files

Pour plus d’informations sur les objectifs de scalabilité et de performances des fichiers Azure et d’Azure File Sync, consultez [Objectifs de scalabilité et de performances des fichiers Azure](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Les limites d’un compte de stockage s’appliquent à tous les partages. Vous pouvez scale up des comptes de stockage au maximum uniquement si vous disposez d’un seul partage par compte de stockage.
>
> Les partages de fichiers standard supérieurs à 5 Tio présentent certaines limitations et restrictions régionales.
> Pour obtenir la liste des limitations, des informations régionales et des instructions permettant d’activer ces tailles de partage de fichiers plus volumineuses, consultez la section [Intégrer à des partages de fichiers plus grands (niveau Standard)](../files/storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) du guide de planification Azure Files.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Objectifs de mise à l’échelle des fichiers Premium

Il existe trois catégories de limitations à prendre en compte pour les fichiers Premium : les comptes de stockage, les partages et les fichiers.

Par exemple :  Un partage unique peut atteindre 100 000 IOPS, et un seul fichier peut scale up jusqu’à 5 000 IOPS. Par exemple, si vous avez trois fichiers dans un partage, celui-ci peut vous permettre d’obtenir jusqu’à 15 000 IOPS.

#### <a name="premium-file-share-limits"></a>Limites de partage de fichiers Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Objectifs de mise à l’échelle d’Azure File Sync

Azure File Sync a été conçu pour proposer un usage illimité, mais cela n’est pas toujours possible. Le tableau suivant indique les limites de tests réalisés par Microsoft, ainsi que les cibles constituant des limites matérielles :

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage File d’attente Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Table Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Voir aussi

- [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Abonnement Azure et limites, quotas et contraintes du service](../../azure-subscription-service-limits.md)
- [Réplication Azure Storage](../storage-redundancy.md)
- [Liste de contrôle des performances et de l’évolutivité de Microsoft Azure Storage](../storage-performance-checklist.md)
