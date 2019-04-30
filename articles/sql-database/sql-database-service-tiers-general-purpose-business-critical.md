---
title: Azure SQL Database - Usage général et Critique pour l’entreprise | Microsoft Docs
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
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: 6a027e6c0afd526139484240f288205fa946d557
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111797"
---
# <a name="azure-sql-database-service-tiers"></a>Niveaux de service Azure SQL Database

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server. Celle-ci est ajustée pour l’environnement cloud afin de garantir une disponibilité de 99,99 % même en cas de panne d’infrastructure. Trois modèles d’architecture sont utilisés dans Azure SQL Database :

- [Usage général](sql-database-service-tier-general-purpose.md), conçu pour la plupart des charges de travail génériques.
- [Critique pour l’entreprise](sql-database-service-tier-business-critical.md), conçu pour les charges de travail à faible latence avec un seul réplica lisible.
- [Hyperscale](sql-database-service-tier-hyperscale.md), conçu pour les bases de données très volumineuses (jusqu’à 100 To) avec plusieurs réplicas lisibles.

Cet article décrit les considérations relatives au stockage et à la sauvegarde pour les niveaux de service Usage général et Critique pour l’entreprise dans le modèle d’achat vCore.

> [!NOTE]
> Pour plus d’informations sur le niveau de service Hyperscale dans le modèle d’achat vCore, consultez [Niveau de service Hyperscale](sql-database-service-tier-hyperscale.md). Pour obtenir une comparaison du modèle d’achat vCore avec le modèle d’achat DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Stockage des données et des journaux

Tenez compte des éléments suivants :

- Le stockage alloué est utilisé par les fichiers de données (MDF) et les fichiers journaux (LDF).
- Chaque taille de calcul d’une base de données unique accepte une taille de base de données maximale, qui correspond, par défaut, à 32 Go.
- Lorsque vous configurez la taille de base de données unique nécessaire (c’est-à-dire, la taille des fichiers MDF), 30 % de stockage supplémentaire sont automatiquement ajoutés pour prendre en charge les fichiers LDF
- La taille de stockage dans une instance gérée doit être spécifiée en multiples de 32 Go.
- Vous pouvez choisir n’importe quelle taille de base de données unique située entre 10 Go et la taille maximale prise en charge.
  - Pour le stockage des niveaux de service Standard et Usage général, augmentez ou diminuez la taille par incrément de 10 Go.
  - Pour le stockage des niveaux de service Premium et Critique pour l’entreprise, augmentez ou diminuez la taille par incrément de 250 Go.
- Dans le niveau de service Usage général, `tempdb` utilise un disque SSD attaché et le coût de ce stockage est inclus dans le prix du modèle vCore.
- Dans le niveau de service Critique pour l’entreprise, `tempdb` partage le disque SSD attaché avec les fichiers MDF et LDF, et le coût du stockage tempDB est inclus dans le prix du modèle vCore.

> [!IMPORTANT]
> Le stockage total alloué aux fichiers MDF et LDF vous est facturé.

Pour surveiller la taille totale actuelle des fichiers MDF et LDF, utilisez [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Pour surveiller la taille actuelle de chaque fichier MDF et LDF, utilisez [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Du stockage de sauvegardes de base de données est alloué pour prendre en charge les fonctionnalités Limite de restauration dans le temps et [Rétention à long terme](sql-database-long-term-retention.md) de SQL Database. Ce stockage est alloué séparément pour chaque base de données. De plus, les fonctionnalités Limite de restauration dans le temps et Conservation à long terme sont, elles aussi, facturées séparément.

- **Limite de restauration dans le temps** : les sauvegardes de bases de données individuelles sont automatiquement copiées vers le [stockage RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md). La taille de stockage augmente dynamiquement avec chaque nouvelle création de sauvegarde.  Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux copiés toutes les 5 minutes. La consommation du stockage dépend du taux de change de la base de données et de la période de rétention. Vous pouvez configurer une période de rétention distincte pour chaque base de données, allant de 7 à 35 jours. Une quantité de stockage minimal correspondant à 1 fois la taille des données est fourni sans frais supplémentaires. Pour la plupart des bases de données, cette quantité est suffisante pour stocker l’équivalent de 7 jours de sauvegardes.
- **Rétention à long terme** : SQL Database permet de configurer une rétention à long terme des sauvegardes complètes d’une durée de 10 ans. Si la stratégie de rétention à long terme est activée, ces sauvegardes sont stockées automatiquement dans le stockage RA-GRS. Toutefois, vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. Cette configuration définit la quantité de stockage utilisée pour les sauvegardes de rétention à long terme. Vous pouvez utiliser la calculatrice de prix LTR pour estimer le coût du stockage de rétention à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage spécifiques disponibles pour une base de données unique dans les niveaux de service Usage général et Critique pour l’entreprise, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage spécifiques disponibles pour les pools élastiques dans les niveaux de service Usage général et Critique pour l’entreprise, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
