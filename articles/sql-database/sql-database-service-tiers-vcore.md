---
title: Service Azure SQL Database - vCore | Microsoft Docs
description: Le modèle d’achat vCore vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/15/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: e833cb0e7f98933fd106a92a9aac6c4c2677d50d
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42443580"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Choix du niveau de service et des ressources de calcul, de mémoire, de stockage et d’E/S pour vCore

Le modèle d’achat vCore vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Il vous permet également de choisir la génération de matériel :
- Gen 4 : jusqu'à 24 processeurs logiques basés sur le processeur Intel E5-2673 v3 (Haswell) de 2,4 GHz, vCore = 1 PP (cœur physique), 7 Go par cœur, disque SSD attaché
- Gen 5 : jusqu'à 80 processeurs logiques basés sur le processeur Intel E5-2673 v4 (Broadwell) de 2,3 GHz, vCore = 1 LP (hyper-thread), 5.5. Go par cœur, disque SSD fast eNVM

Le modèle vCore vous permet également d’utiliser [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) afin de réduire les coûts.

## <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

Le modèle vCore fournit deux niveaux de service, Usage général et Critique pour l’entreprise. Chaque niveau de service a son propre niveau de performance, la conception de sa haute disponibilité, son isolation des défaillances, ses types de stockage et sa plage d’E/S. Le client doit configurer séparément le stockage nécessaire et la période de rétention des sauvegardes.

Le tableau suivant montre les différences entre ces deux niveaux :

||**Usage général**|**Critique pour l’entreprise**|
|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques.|Applications métier avec besoins en E/S élevés. Offre la meilleure résilience aux échecs en utilisant plusieurs répliques isolées.|
|Calcul|Gen4 : 1 à 24 vCore<br/>Gen5 : 1 à 80 vCore|Gen4 : 1 à 24 vCore<br/>Gen5 : 1 à 80 vCore|
|Mémoire|Gen4 : 7 Go par cœur<br>Gen5 : 5,5 Go par cœur | Gen4 : 7 Go par cœur<br>Gen5 : 5,5 Go par cœur |
|Stockage|[Stockage distant Premium](../virtual-machines/windows/premium-storage.md),<br/>Base de données singleton : 5 Go à 4 To<br/>Instance gérée : 32 Go à 8 To |Stockage SSD local,<br/>Base de données unique : 5 Go à 4 To<br/>Instance gérée : 32 Go à 4 To |
|Débit d’E/S (approximatif)|Base de données singleton : 500 IOPS par vCore avec un maximum de 7000 IOPS</br>Instance gérée : dépend de la [taille du fichier](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS par cœur avec 200 000 IOPS au maximum|
|Disponibilité|1 réplica, sans échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](sql-database-read-scale-out.md),<br/>Haute disponibilité redondante dans une zone|
|Sauvegardes|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|
|En mémoire|N/A|Pris en charge|
|||

Pour plus d’informations, consultez [Limites de ressources vCore dans une base de données Singleton](sql-database-vcore-resource-limits-single-databases.md) et [Limites de ressources vCore dans une instance gérée](sql-database-managed-instance.md#vcore-based-purchasing-model). 

> [!IMPORTANT]
> Si vous avez besoin d’une capacité de calcul inférieure à 1 vCore, utilisez le modèle d’achat DTU.

Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées. 

## <a name="storage-considerations"></a>Considérations relatives au stockage

Tenez compte des éléments suivants :
- Le stockage alloué est utilisé par les fichiers de données (MDF) et les fichiers journaux (LDF).
- Chaque niveau de performance d’une base de données Singleton accepte une taille de base de données maximale, qui correspond, par défaut, à 32 Go.
- Lorsque vous configurez la taille de base de données Singleton nécessaire (c’est-à-dire, la taille des fichiers MDF), 30 % de stockage supplémentaire sont automatiquement ajoutés pour prendre en charge les fichiers LDF
- La taille de stockage dans une instance gérée doit être spécifiée en multiples de 32 Go.
- Vous pouvez choisir n’importe quelle taille de base de données Singleton située entre 10 Go et la taille maximale prise en charge
 - Pour le stockage Standard, augmentez ou diminuez la taille par incréments de 10 Go
 - Pour le stockage Premium, augmentez ou diminuez la taille par incréments de 250 Go
- Dans le niveau de service Usage général, `tempdb` utilise un disque SSD attaché et le coût de ce stockage est inclus dans le prix du modèle vCore.
- Dans le niveau de service Critique pour l’entreprise, `tempdb` partage le disque SSD attaché avec les fichiers MDF et LDF, et le coût du stockage tempDB est inclus dans le prix du modèle vCore.

> [!IMPORTANT]
> Le stockage total alloué aux fichiers MDF et LDF vous est facturé.

Pour surveiller la taille totale actuelle des fichiers MDF et LDF, utilisez [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Pour surveiller la taille actuelle de chaque fichier MDF et LDF, utilisez [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Du stockage de sauvegardes de base de données est alloué pour prendre en charge les fonctionnalités Limite de restauration dans le temps et [Rétention à long terme](sql-database-long-term-retention.md) de SQL Database. Ce stockage est alloué séparément pour chaque base de données. De plus, les fonctionnalités Limite de restauration dans le temps et Conservation à long terme sont, elles aussi, facturées séparément. 

- **Limite de restauration dans le temps** : les sauvegardes de bases de données sont automatiquement copiées vers le [stockage RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md). La taille de stockage augmente dynamiquement avec chaque nouvelle création de sauvegarde.  Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux copiés toutes les 5 minutes. La consommation du stockage dépend du taux de change de la base de données et de la période de rétention. Vous pouvez configurer une période de rétention distincte pour chaque base de données, allant de 7 à 35 jours. Un volume de stockage minimal correspondant à la taille des données est fourni sans frais supplémentaires. Pour la plupart des bases de données, cette quantité est suffisante pour stocker l’équivalent de 7 jours de sauvegardes.
- **Rétention à long terme** : SQL Database permet de configurer une rétention à long terme des sauvegardes complètes d’une durée de 10 ans. Si la stratégie de rétention à long terme est activée, ces sauvegardes sont stockées automatiquement dans le stockage RA-GRS. Toutefois, vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. Cette configuration définit la quantité de stockage utilisée pour les sauvegardes de rétention à long terme. Vous pouvez utiliser la calculatrice de prix LTR pour estimer le coût du stockage de rétention à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Dans le modèle d’achat vCore, vous pouvez échanger vos licences existantes avec des tarifs réduits sur SQL Database, en utilisant [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Cet avantage Azure vous permet d’utiliser vos licences SQL Server locales pour économiser jusqu’à 30 % sur Azure SQL Database, avec Software Assurance.

![Prix](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migration du modèle DTU vers le modèle vCore

### <a name="migration-of-single-databases-with-geo-replication-links"></a>Migration de bases de données uniques avec des liens de géoréplication

La migration du modèle DTU vers le modèle vCore (et inversement) est similaire à la mise à niveau (ou à la rétrogradation) des relations de géoréplication entre les bases de données Standard et Premium. Cela ne nécessite pas l’arrêt de la géoréplication, toutefois, l’utilisateur doit respecter les règles de séquencement. Lors d’une mise à niveau, vous devez mettre à niveau la base de données secondaire, avant de mettre à niveau la base de données primaire. Lors d’une rétrogradation, inversez l’ordre : rétrogradez d’abord la base de données primaire, puis la base de données secondaire. 

Lorsque vous utilisez la géoréplication entre deux pools élastiques, il est recommandé de désigner un pool comme le pool principal et l’autre comme le pool secondaire. Dans ce cas, utilisez les mêmes recommandations pour la migration des pools élastiques.  Toutefois, il est techniquement possible qu’un pool élastique contienne à la fois la base de données primaire et la base de données secondaire. Dans ce cas, pour effectuer correctement la migration, vous devez désigner le pool ayant l’utilisation la plus élevée comme étant le pool principal, et suivre les règles de séquencement.  

Le tableau suivant fournit des conseils pour certains scénarios de migration : 

|Niveau de service actuel|Niveau de service cible|Type de migration|Actions utilisateur|
|---|---|---|---|
|standard|Usage général|Latéral|Peut effectuer la migration dans n’importe quel ordre, mais doit garantir un redimensionnement vCore adapté*|
|Premium|Critique pour l’entreprise|Latéral|Peut effectuer la migration dans n’importe quel ordre, mais doit garantir un redimensionnement vCore adapté*|
|standard|Critique pour l’entreprise|Mise à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
|Critique pour l’entreprise|standard|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Premium|Usage général|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Usage général|Premium|Mise à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
|Critique pour l’entreprise|Usage général|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Usage général|Critique pour l’entreprise|Mise à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
||||

\* Chaque groupe de 100 DTU du niveau Standard nécessite au moins 1 vCore, et chaque groupe de 125 DTU du niveau Premium nécessite au moins 1 vCore

### <a name="migration-of-failover-groups"></a>Migration des groupes de basculement 

La migration des groupes de basculement comprenant plusieurs bases de données nécessite que la base de données primaire et la base de données secondaire soient migrées séparément. Pendant ce processus, les mêmes recommandations et règles de séquencement s’appliquent. Une fois les bases de données converties au modèle vCore, le groupe de basculement reste actif, avec les mêmes paramètres de stratégie. 

### <a name="creation-of-a-geo-replication-secondary"></a>Création d’une base de données secondaire de géoréplication

Pour créer une base de données secondaire de géoréplication, vous devez utiliser le même niveau de service que la base de données primaire. Pour les bases de données avec un taux élevé de génération de journaux, il est recommandé de créer la base de données secondaire avec le même niveau de performance que la base de données primaire. Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire, il est conseillé que le paramètre `maxVCore` du pool corresponde au niveau de performance de la base de données primaire. Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire située dans un autre pool élastique, il est conseillé d’attribuer la même valeur au paramètre `maxVCore` des deux pools.

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utilisation de la copie de base de données pour convertir une base de données DTU en base de données vCore

Vous pouvez copier n’importe quelle base de données avec niveau de performance DTU vers une base de données avec niveau de performance vCore, sans aucune restriction ni séquencement spécial, tant que le niveau de performance cible prend en charge la taille maximale de la base de données source. En effet, la copie de base de données crée un instantané des données dès que commence l’opération de copie, et elle n’effectue pas de synchronisation des données entre la source et la cible. 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les pools élastiques, consultez [Limites des ressources vCoreCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
