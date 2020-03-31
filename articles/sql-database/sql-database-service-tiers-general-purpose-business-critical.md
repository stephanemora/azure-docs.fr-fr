---
title: Usage général et Critique pour l’entreprise
description: L’article aborde les niveaux de service Usage général et Critique pour l’entreprise dans le modèle d’achat vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937837"
---
# <a name="azure-sql-database-service-tiers"></a>Niveaux de service Azure SQL Database

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server qui est ajustée pour l’environnement cloud afin de garantir une disponibilité de 99,99 pour cent même en cas de panne d’infrastructure. Trois niveaux de service sont utilisés dans Azure SQL Database, chacun doté d’un modèle architectural distinct. Ces niveaux de service sont les suivants :

- [Usage général](sql-database-service-tier-general-purpose.md), conçu pour les charges de travail axées sur le budget.
- [Hyperscale](sql-database-service-tier-hyperscale.md), conçu pour la plupart des charges de travail métier, offrant un stockage hautement scalable, un scale-out en lecture et des fonctionnalités de restauration rapide de base de données.
- [Critique pour l’entreprise](sql-database-service-tier-business-critical.md), conçu pour les charges de travail à faible latence avec une haute résilience aux défaillances et des basculements rapides.

Cet article présente les différences entre les niveaux de service, les considérations relatives au stockage et à la sauvegarde pour les niveaux de service Usage général et Critique pour l’entreprise dans le modèle d’achat vCore.

## <a name="service-tier-comparison"></a>Comparaison des niveaux de service

Le tableau suivant décrit les principales différences entre les niveaux de service pour la dernière génération (Gen5). Notez que les caractéristiques des niveaux de service peuvent être différentes dans Single Database et Managed Instance.

| | Type de ressource | Usage général |  Hyperscale | Critique pour l’entreprise |
|:---:|:---:|:---:|:---:|:---:|
| **Idéal pour** | |  Offre des options de calcul et de stockage équilibrées et économiques. | La plupart des charges de travail d’entreprise. Mise à l’échelle automatique de la taille de stockage jusqu’à 100 To, mise à l’échelle verticale et horizontale du calcul, restauration rapide de la base de données. | Applications OLTP avec des débits de transactions élevés et une faible latence des E/S. Offre une meilleure résilience aux défaillances et des basculements rapides à l’aide de plusieurs réplicas mis à jour de façon synchrone.|
|  **Disponible dans le type de ressource :** ||Base de données unique / pool élastique / instance managée | Base de données unique | Base de données unique / pool élastique / instance managée |
| **Taille de calcul**|Base de données unique / pool élastique | 1 à 80 cœurs virtuels | 1 à 80 vCores | 1 à 80 cœurs virtuels |
| | Instance gérée | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Pools Managed Instance | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/A | N/A |
| **Type de stockage** | Tous | Stockage distant Premium (par instance) | Stockage découplé avec cache disque SSD local (par instance) | Stockage SSD local ultra-rapide (par instance) |
| **Taille de la base de données** | Base de données unique / pool élastique | 5 Go - 4 To | Jusqu’à 100 To | 5 Go - 4 To |
| | Instance gérée  | 32 Go - 8 To | N/A | 32 Go - 4 To |
| **Taille de stockage** | Base de données unique / pool élastique | 5 Go - 4 To | Jusqu’à 100 To | 5 Go - 4 To |
| | Instance gérée  | 32 Go - 8 To | N/A | 32 Go - 4 To |
| **Taille de TempDB** | Base de données unique / pool élastique | [32 Go par vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 Go par vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 Go par vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instance gérée  | [24 Go par vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | Jusqu’à 4 To - [limité par taille de stockage](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Débit d’écriture des journaux** | Base de données unique | [1,875 Mo/s par vCore (30 Mo/s max)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 Mo/s | [6 Mo/s par vCore (96 Mo/s max)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instance gérée | [3 Mo/s par vCore (22 Mo/s max)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [4 Mo/s par vCore (48 Mo/s max)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Disponibilité**|Tous| 99,99 % |  [99,95 % avec un réplica secondaire, 99,99 % avec d’autres réplicas](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995 % avec base de données unique et redondante dans une zone](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Sauvegardes**|Tous|RA-GRS, 7 à 35 jours (7 jours par défaut)| RA-GRS, 7 jours, récupération jusqu’à une date et heure (PITR) à durée constante | RA-GRS, 7 à 35 jours (7 jours par défaut) |
|**OLTP en mémoire** | | N/A | N/A | Disponible |
|**Réplicas en lecture seule**| | 0 intégré <br> 0 à 4 utilisant la [géoréplication](sql-database-active-geo-replication.md) | 0 à 4 intégrés | 1 intégré et inclus dans le prix <br> 0 à 4 utilisant la [géoréplication](sql-database-active-geo-replication.md) |
|**Tarification/facturation** | Base de données unique | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/single/) sont facturés. <br/>Les IOPS ne sont pas facturées. | [vCore pour chaque réplica et le stockage utilisé](https://azure.microsoft.com/pricing/details/sql-database/single/) sont facturés. <br/>IOPS non encore facturées. | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/single/) sont facturés. <br/>Les IOPS ne sont pas facturées. |
|| Instance gérée | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/managed/) sont facturés. <br/>Les IOPS ne sont pas facturées.| N/A | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/managed/) sont facturés. <br/>Les IOPS ne sont pas facturées.| 
|**Modèles de remise**| | [Instances réservées](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (non disponible avec les abonnements dev/test)<br/>Abonnements Dev/Test [Entreprise](https://azure.microsoft.com/offers/ms-azr-0148p/) et [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (non disponible avec les abonnements dev/test)<br/>Abonnements Dev/Test [Entreprise](https://azure.microsoft.com/offers/ms-azr-0148p/) et [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Instances réservées](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (non disponible avec les abonnements dev/test)<br/>Abonnements Dev/Test [Entreprise](https://azure.microsoft.com/offers/ms-azr-0148p/) et [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Pour plus d’informations, consultez les détails des différences entre les niveaux de service dans les pages [Base de données unique (vCore)](sql-database-vcore-resource-limits-single-databases.md), [Pools de bases de données uniques (VCore)](sql-database-dtu-resource-limits-single-databases.md), [Base de données unique (UDBD)](sql-database-dtu-resource-limits-single-databases.md), [Pools de bases de données uniques (UDBD)](sql-database-dtu-resource-limits-single-databases.md) et [Instance gérée](sql-database-managed-instance-resource-limits.md).

> [!NOTE]
> Pour plus d’informations sur le niveau de service Hyperscale dans le modèle d’achat vCore, consultez [Niveau de service hyperscale](sql-database-service-tier-hyperscale.md). Pour obtenir une comparaison du modèle d’achat vCore avec le modèle d’achat DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Stockage des données et des journaux

Les facteurs suivants affectent la quantité de stockage utilisée pour les fichiers de données et les fichiers journaux, et s’appliquent aux niveaux Usage général et Critique pour l’entreprise. Pour plus d’informations sur le stockage des données et des journaux dans Hyperscale, consultez [Niveau de service Hyperscale](sql-database-service-tier-hyperscale.md).

- Le stockage alloué est utilisé par les fichiers de données (MDF) et les fichiers journaux (LDF).
- Chaque taille de calcul d’une base de données unique accepte une taille de base de données maximale, qui correspond, par défaut, à 32 Go.
- Lorsque vous configurez la taille de base de données unique nécessaire (la taille du fichier MDF), 30 pour cent de stockage supplémentaire sont automatiquement ajoutés pour prendre en charge les fichiers LDF.
- La taille de stockage d’une instance managée SQL Database doit être spécifiée en multiples de 32 Go.
- Vous pouvez choisir n’importe quelle taille de base de données unique située entre 10 Go et la taille maximale prise en charge.
  - Pour le stockage des niveaux de service Standard et Usage général, augmentez ou diminuez la taille par incrément de 10 Go.
  - Pour le stockage des niveaux de service premium et critique pour l’entreprise, augmentez ou diminuez la taille par incrément de 250 Go.
- Dans le niveau de service usage général, `tempdb` utilise un disque SSD attaché et le coût de ce stockage est inclus dans le prix du modèle vCore.
- Dans le niveau de service Critique pour l’entreprise, `tempdb` partage le disque SSD attaché avec les fichiers MDF et LDF, et le coût du stockage `tempdb` est inclus dans le prix du modèle vCore.

> [!IMPORTANT]
> Le stockage total alloué aux fichiers MDF et LDF vous est facturé.

Pour surveiller la taille totale actuelle de vos fichiers MDF et LDF, utilisez [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Pour surveiller la taille actuelle de chaque fichier MDF et LDF, utilisez [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Du stockage de sauvegardes de base de données est alloué pour prendre en charge les fonctionnalités Limite de restauration dans le temps et [Rétention à long terme](sql-database-long-term-retention.md) de SQL Database. Ce stockage est alloué séparément pour chaque base de données. De plus, les fonctionnalités Limite de restauration dans le temps et Conservation à long terme sont, elles aussi, facturées séparément.

- **Limite de restauration dans le temps** : les sauvegardes de bases de données individuelles sont automatiquement copiées vers le [stockage géographiquement redondant avec accès en lecture (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md). La taille de stockage augmente dynamiquement avec chaque nouvelle création de sauvegarde. Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux qui sont copiés toutes les 5 minutes. La consommation du stockage dépend du taux de change de la base de données et de la période de rétention pour les sauvegardes. Vous pouvez configurer une période de rétention distincte pour chaque base de données, allant de 7 à 35 jours. Un volume de stockage minimal égal à 100 pour cent (1x) de la taille de la base de données est fourni sans frais supplémentaires. Pour la plupart des bases de données, cette quantité est suffisante pour stocker l’équivalent de 7 jours de sauvegardes.
- **Rétention à long terme** : SQL Database vous permet de configurer une conservation à long terme des sauvegardes complètes d’une durée de 10 ans. Si vous configurez une stratégie de rétention à long terme, ces sauvegardes sont stockées automatiquement dans le stockage RA-GRS. Toutefois, vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. La configuration que vous choisissez détermine la quantité de stockage utilisée pour les sauvegardes de rétention à long terme. Pour estimer le coût du stockage de rétention à long terme, vous pouvez utiliser la calculatrice de prix LTR. Pour plus d’informations, consultez [Rétention à long terme de SQL Database](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage spécifiques disponibles pour une base de données unique dans les niveaux de service usage général et critique pour l’entreprise, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage spécifiques disponibles pour les pools élastiques dans les niveaux de service usage général et critique pour l’entreprise, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).
