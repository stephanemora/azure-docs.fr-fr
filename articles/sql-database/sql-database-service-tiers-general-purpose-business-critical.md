---
title: Azure SQL Database - Usage général et Critique pour l’entreprise | Microsoft Docs
description: L’article aborde les niveaux de service critique d’entreprise dans le modèle d’achat vCore à usage général.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431332"
---
# <a name="azure-sql-database-service-tiers"></a>Niveaux de service Azure SQL Database

Base de données SQL Azure est basé sur l’architecture du moteur de base de données SQL Server qui est ajustée pour l’environnement de cloud garantir la disponibilité de 99,99 %, même s’il existe une défaillance de l’infrastructure. Trois niveaux de service sont utilisés dans Azure SQL Database, chacun avec un autre modèle architectural. Ces niveaux de service sont les suivants :

- [Usage général](sql-database-service-tier-general-purpose.md), qui est conçu pour les charges de travail plus génériques.
- [Critique pour l’entreprise](sql-database-service-tier-business-critical.md), qui est conçu pour les charges de travail à faible latence avec un seul réplica accessible en lecture.
- [Hyperscale](sql-database-service-tier-hyperscale.md), qui est conçu pour les bases de données très volumineuses (jusqu'à 100 To) avec plusieurs réplicas lisibles.

Cet article traite des considérations relatives à la sauvegarde et de stockage pour les niveaux de service critique d’entreprise dans le modèle d’achat vCore à usage général.

> [!NOTE]
> Pour plus d’informations sur le niveau de service de très grande échelle dans le modèle d’achat vCore, consultez [niveau de service hyperscale](sql-database-service-tier-hyperscale.md). Pour obtenir une comparaison du modèle d’achat vCore avec le modèle d’achat DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Stockage des données et des journaux

Les facteurs suivants affectent la quantité de stockage utilisé pour les données et les fichiers journaux :

- Le stockage alloué est utilisé par les fichiers de données (MDF) et des fichiers journaux (LDF).
- Chaque base de données unique de calcul prend en charge de la taille une taille maximale de la base de données, avec une taille maximale de 32 Go.
- Lorsque vous configurez la taille de la base de données unique requis (la taille du fichier MDF), 30 pour cent plus un stockage supplémentaire est ajouté automatiquement pour prendre en charge les fichiers LDF.
- La taille de stockage pour une instance gérée de la base de données SQL doit être spécifiée en multiples de 32 Go.
- Vous pouvez sélectionner n’importe quelle taille de base de données unique entre 10 Go et le nombre maximal pris en charge.
  - Pour le stockage dans les niveaux de service standard ou à usage général, augmentez ou diminuez la taille par incréments de 10 Go.
  - Pour le stockage premium ou niveaux de service critique d’entreprise, augmenter ou diminuer la taille par incréments de 250 Go.
- Dans le niveau de service usage général, `tempdb` utilise un disque SSD attaché et le coût de ce stockage est inclus dans le prix de vCore.
- Dans le niveau de service critique métier, `tempdb` partage le disque SSD attaché avec les fichiers MDF et LDF et le `tempdb` coût de stockage est inclus dans le prix de vCore.

> [!IMPORTANT]
> Vous êtes facturé pour le stockage total alloué aux fichiers MDF et LDF.

Pour surveiller la taille totale actuelle de vos fichiers MDF et LDF, utilisez [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Pour surveiller la taille actuelle de chaque fichier MDF et LDF, utilisez [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Stockage pour les sauvegardes de base de données est alloué pour prendre en charge la restauration de point-à-temps (PITR) et [rétention à long terme (LTR)](sql-database-long-term-retention.md) les fonctionnalités de base de données SQL. Ce stockage est alloué séparément pour chaque base de données. De plus, les fonctionnalités Limite de restauration dans le temps et Conservation à long terme sont, elles aussi, facturées séparément.

- **Limite de restauration dans le temps** : Sauvegardes de base de données individuels sont copiés vers [accès en lecture stockage géo-redondant (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatiquement. La taille de stockage augmente dynamiquement à mesure que les nouvelles sauvegardes sont créées. Le stockage est utilisé par les sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et sauvegardes de journal des transactions, qui sont copiés toutes les 5 minutes. La consommation de stockage dépend de la fréquence de modification de la base de données et la période de rétention pour les sauvegardes. Vous pouvez configurer une période de rétention distincte pour chaque base de données, allant de 7 à 35 jours. Un volume de stockage minimal égal à 100 pour cent (1 x) de la taille de la base de données est fourni sans frais supplémentaires. Pour la plupart des bases de données, cette quantité est suffisante pour stocker l’équivalent de 7 jours de sauvegardes.
- **Rétention à long terme** : Base de données SQL vous offre la possibilité de configurer la rétention à long terme des sauvegardes complètes pendant 10 ans. Si vous configurez une stratégie de rétention à long terme, ces sauvegardes sont stockées dans le stockage RA-GRS automatiquement, mais vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux exigences de conformité différents, vous pouvez sélectionner différentes périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. La configuration que vous choisissez détermine la quantité de stockage doit être utilisé pour les sauvegardes de rétention à long terme. Pour estimer le coût du stockage de rétention à long terme, vous pouvez utiliser la calculatrice de prix de rétention à long terme. Pour plus d’informations, consultez [rétention à long terme de base de données SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la spécifique de calcul tailles et les tailles de stockage disponibles pour une base de données dans les niveaux de service critique d’entreprise à usage général, consultez [limites de ressources VCORE de base de données SQL pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).
- Pour plus d’informations sur la spécifique de calcul tailles et les tailles de stockage disponibles pour les pools élastiques dans les niveaux de service critique d’entreprise à usage général, consultez [limites de ressources VCORE de base de données SQL pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).
