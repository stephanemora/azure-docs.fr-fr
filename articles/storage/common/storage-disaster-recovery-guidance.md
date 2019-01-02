---
title: Que faire en cas de panne d’Azure Storage | Microsoft Docs
description: Que faire en cas de panne d’Azure Storage
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 39a938d45c8f15c21b44bb5b04b1429fb4733b5a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323266"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Que faire en cas de panne d’Azure Storage
Microsoft s’engage à déployer tous les efforts nécessaires pour garantir en permanence la disponibilité de ses services. Il arrive parfois que des phénomènes incontrôlables entraînent des interruptions de service non planifiés dans une ou plusieurs régions. Pour vous aider à faire face à ces rares occurrences, vous trouverez ici quelques conseils généraux pour les services Azure Storage.

## <a name="how-to-prepare"></a>Préparation
Il est essentiel que chaque client veille à élaborer son propre plan de récupération d’urgence. L’effort de récupération suite à une panne de stockage implique généralement l’intervention du personnel opérationnel ainsi que l’application de procédures automatisées afin de rétablir le bon fonctionnement de vos applications. Reportez-vous à la documentation Azure ci-dessous pour créer votre propre plan de récupération d’urgence :

* [Liste de contrôle de disponibilité](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Conception d’applications résilientes pour Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Service Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Réplication Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Service Azure Backup](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Mode de détection
Pour déterminer l’état du service Azure, il est recommandé de s’abonner au [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Que faire en cas de panne d’Azure Storage
Si un ou plusieurs services Azure Storage sont provisoirement indisponibles dans une ou plusieurs régions, deux options s’offrent à vous. Si vous souhaitez un accès immédiat à vos données, envisagez l’Option 2.

### <a name="option-1-wait-for-recovery"></a>Option 1 : Attendre la récupération
Dans ce cas, aucune action n’est requise de votre part. Nous travaillons assidûment à la restauration de la disponibilité du service Azure. Vous pouvez analyser l’état actuel du service dans le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Option 2 : Copier les données à partir de la base de données secondaire
Si vous avez choisi le [stockage géo-redondant avec accès en lecture (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (recommandé) pour vos comptes de stockage, vous bénéficierez d’un accès en lecture à vos données à partir de la région secondaire. Vous pouvez utiliser des outils tels que [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) et la [bibliothèque de déplacement des données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) pour copier des données de la région secondaire dans un autre compte de stockage situé dans une région non compromise, puis pointer vos applications vers ce compte de stockage pour bénéficier d’un accès en lecture et en écriture.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Que se passe-t-il en cas de basculement d’Azure Storage ?
Si vous avez choisi le [stockage géo-redondant (GRS)](storage-redundancy-grs.md) ou le [stockage géo-redondant avec accès en lecture (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (recommandé), Azure Storage conservera vos données dans deux régions (primaire et secondaire). Azure Storage conserve constamment plusieurs réplicas de vos données dans les deux régions.

Quand un sinistre régional affecte votre région primaire, nous tentons d’abord de restaurer le service dans la région qui fournit la meilleure combinaison d’objectif de délai de récupération (RTO) et d’objectif de point de récupération (RPO). Selon de la nature de l’incident et son impact, il peut arriver dans de rares circonstances que ne soyons pas en mesure de restaurer la région primaire. À ce stade, nous procéderons à un basculement géographique. La réplication de données inter-régions est un processus asynchrone qui peut nécessiter un certain délai. Il est donc possible que les modifications qui n’ont pas encore été répliquées dans la région secondaire soient perdues.

Quelques points relatifs à l’expérience de basculement géographique du stockage :

* Le basculement géographique du stockage est déclenché uniquement par l’équipe Azure Storage et ne nécessite donc aucune intervention du client. Le basculement est déclenché lorsque l’équipe du Stockage Azure a épuisé toutes les options de restauration des données dans la région qui fournit la meilleure combinaison d’objectifs RTO et RPO.
* Vos points de terminaison de service de stockage existant pour les objets blob, les tables, les files d’attente et les fichiers restent les mêmes après le basculement ; l’entrée DNS fournie par Microsoft devra être mise à jour pour basculer de la région primaire à la région secondaire. Microsoft effectue cette mise à jour automatiquement dans le cadre du processus de basculement géographique.
* Avant et pendant le basculement géographique, vous n’avez pas d’accès en écriture à votre compte de stockage en raison de l’impact de l’incident, mais vous pouvez toujours lire les données à partir de la base de données secondaire si votre compte de stockage a été configuré en tant que RA-GRS.
* Une fois le basculement géographique effectué et les modifications DNS propagées, l’accès en lecture-écriture à votre compte de stockage est restauré si vous disposez d’un stockage géoredondant (GRS) ou d’un stockage géographiquement redondant avec accès en lecture (RA-GRS). Le point de terminaison qui était jusque-là votre point de terminaison secondaire devient votre point de terminaison principal. 
* Vous pouvez vérifier l’état de l’emplacement principal et envoyer une requête pour connaître la date et l’heure du dernier basculement géographique de votre compte de stockage. Pour plus d’informations, consultez [Comptes de stockage - Obtenir les propriétés](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/getproperties).
* Après le basculement, votre compte de stockage est entièrement opérationnel, mais son état est « dégradé », car il est hébergé dans une région autonome sans aucune géoréplication possible. Pour atténuer ce risque, nous restaurerons la région primaire d’origine et effectuerons une géo-restauration afin de restaurer l’état d’origine. Si la région primaire d’origine est irrécupérable, nous allouerons une autre région secondaire.

## <a name="best-practices-for-protecting-your-data"></a>Meilleures pratiques pour la protection de vos données
Il existe des approches recommandées pour sauvegarder régulièrement vos données de stockage.

* Disques de machine virtuelle : utilisez le [service Azure Backup](https://azure.microsoft.com/services/backup/) pour sauvegarder les disques de machine virtuelle utilisées par vos machines virtuelles Azure.
* Objets Blob de blocs : activez la [suppression réversible](../blobs/storage-blob-soft-delete.md) pour protéger contre les suppressions et remplacements au niveau objet, ou copiez les objets blob vers un autre compte de stockage dans une autre région à l’aide d’[AzCopy](storage-use-azcopy.md), d’[Azure PowerShell](storage-powershell-guide-full.md) ou de la [bibliothèque de déplacement de données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Tables: utilisez [AzCopy](storage-use-azcopy.md) pour exporter les données de table vers un autre compte de stockage dans une autre région.
* Fichiers : utilisez [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) pour copier vos fichiers vers un autre compte de stockage dans une autre région.

Pour plus d’informations sur la création d’applications tirant pleinement parti de la fonctionnalité RA-GRS, consultez [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md).
