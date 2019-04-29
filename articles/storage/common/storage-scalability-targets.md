---
title: Performances et évolutivité objectifs du stockage Azure - comptes de stockage
description: En savoir plus sur les cibles d’évolutivité et les performances, y compris la capacité, le taux de demande et la bande passante entrante et sortante, pour les comptes de stockage Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: e3e0e9ae4a1939aad9ab2ae42a1b51b1b00e2462
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101473"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Objectifs de performance et d’extensibilité stockage Azure pour les comptes de stockage

Cet article présente les objectifs de performance et d’évolutivité pour les comptes de stockage Azure. Les objectifs d’extensibilité et de performances répertoriés ici sont des objectifs haut de gamme mais réalisables. Dans tous les cas, le taux de demande et la bande passante atteints par votre compte de stockage dépendent de la taille des objets stockés, des modèles d’accès utilisés et du type de charge de travail de votre application.

Veillez à tester votre service afin de déterminer si ses performances répondent à vos besoins. Dans la mesure du possible, évitez les pics soudains de trafic et assurez-vous que le trafic est bien réparti sur toutes les partitions.

Lorsque votre application atteint la limite de gestion d’une partition concernant la charge de travail, Stockage Azure commence à renvoyer des codes d’erreur 503 (Serveur occupé) ou 500 (Délai d’expiration de l’opération). Si vous rencontrez des erreurs 503, nous vous recommandons de modifier votre application pour utiliser une stratégie d’interruption exponentielle pour les nouvelles tentatives. L’interruption exponentielle diminue la charge sur la partition et atténue les pics de trafic pour cette partition.

## <a name="storage-account-scale-limits"></a>Limites de mise à l’échelle de compte de stockage

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limites de mise à l’échelle du compte de stockage Premium performances

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limites d’évolutivité d’un fournisseur de ressources de stockage

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Blob Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files

Pour plus d’informations sur les objectifs de scalabilité et de performances des fichiers Azure et d’Azure File Sync, consultez [Objectifs de scalabilité et de performances des fichiers Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Fichiers Premium mettre à l’échelle cibles

Il existe trois catégories de limitations à prendre en compte pour les fichiers de premium : comptes de stockage et partages de fichiers.

Par exemple :  Un partage unique peut atteindre 100 000 e/s et un seul fichier peut évoluer jusqu'à 5 000 e/s. Par conséquent, par exemple, si vous avez trois fichiers dans un partage, l’IOPs maximum, vous pouvez obtenir à partir de ce partage est de 15 000.

#### <a name="premium-file-share-limits"></a>Limites de partage de fichiers Premium

> [!IMPORTANT]
> Limites de compte de stockage s’appliquent à tous les partages. Mise à l’échelle jusqu'à la valeur maximale pour les comptes de stockage n’est réalisable s’il en existe qu’un seul partage par compte de stockage.

|Domaine  |Cible  |
|---------|---------|
|Taille min configuré.                        |100 Gio      |
|Nombre maximal configuré de taille                        |100 TiB      |
|Augmenter/diminuer la taille minimale    |1 GiB      |
|IOPS de base    |1 Go jusqu'à 100 000 par seconde|
|E/s de rupture    |3 x IOPS par Gio jusqu'à 100 000|
|Taux de sortie         |60 Mio/s + 0,06 * approvisionné Gio        |
|Taux d’entrée| 40 Mio/s + 0.04 * approvisionné Gio |
|Nombre maximal d’instantanés        |200       |

#### <a name="premium-file-limits"></a>Limites du fichier Premium

|Domaine  |Cible  |
|---------|---------|
|Taille                  |1 Tio         |
|Max IOPS par fichier     |5 000         |
|Handles simultanés    |2 000         |

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