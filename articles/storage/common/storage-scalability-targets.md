---
title: Objectifs de performance et d’extensibilité du Stockage Azure
description: Obtenez plus d’informations sur les objectifs d’extensibilité et de performances, y compris la capacité, le taux de demande et la bande passante entrante et sortante pour les comptes standards d’Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: f8227495d7ca2e1d43b74066d359d0d59e456263
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451204"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Objectifs de performance et évolutivité d’Azure Storage pour les comptes de stockage standards

Cet article présente les objectifs de performance et d’évolutivité pour les comptes d’Azure Storage standards. Les objectifs d’extensibilité et de performances répertoriés ici sont des objectifs haut de gamme mais réalisables. Dans tous les cas, le taux de demande et la bande passante atteints par votre compte de stockage dépendent de la taille des objets stockés, des modèles d’accès utilisés et du type de charge de travail de votre application. 

Veillez à tester votre service afin de déterminer si ses performances répondent à vos besoins. Dans la mesure du possible, évitez les pics soudains de trafic et assurez-vous que le trafic est bien réparti sur toutes les partitions.

Lorsque votre application atteint la limite de gestion d’une partition concernant la charge de travail, Stockage Azure commence à renvoyer des codes d’erreur 503 (Serveur occupé) ou 500 (Délai d’expiration de l’opération). Si vous rencontrez des erreurs 503, nous vous recommandons de modifier votre application pour utiliser une stratégie d’interruption exponentielle pour les nouvelles tentatives. L’interruption exponentielle diminue la charge sur la partition et atténue les pics de trafic pour cette partition.

## <a name="standard-storage-account-scale-limits"></a>Limites d’évolutivité d’un compte de stockage standard
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limites d’évolutivité d’un fournisseur de ressources de stockage 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Blob Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files
Pour plus d’informations sur les objectifs de scalabilité et de performances des fichiers Azure et d’Azure File Sync, consultez [Objectifs de scalabilité et de performances des fichiers Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Objectifs de mise à l’échelle d’Azure File Sync
Azure File Sync a été conçu pour proposer un usage illimité, mais cela n’est pas toujours possible. Le tableau suivant indique les limites de tests réalisés par Microsoft, ainsi que les cibles constituant des limites matérielles :

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage File d’attente Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Table Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Voir aussi
* [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Abonnement Azure et limites, quotas et contraintes du service](../../azure-subscription-service-limits.md)
* [Réplication Azure Storage](../storage-redundancy.md)
* [Liste de contrôle des performances et de l’évolutivité de Microsoft Azure Storage](../storage-performance-checklist.md)

