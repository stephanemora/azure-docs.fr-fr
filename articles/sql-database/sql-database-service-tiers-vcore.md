---
title: Service Azure SQL Database - vCore | Microsoft Docs
description: Découvrez les niveaux de service des bases de données uniques du pool qui permettent de fournir divers niveaux de performance et diverses tailles de stockage.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/14/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: d37bf4fd131e700d4f4c3b07c84754b4014ca228
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648351"
---
# <a name="vcore-based-purchasing-model-for-azure-sql-database-preview"></a>Nouveau modèle d’achat basé sur des vCores d’Azure SQL Database (préversion)

[Azure SQL Database](sql-database-technical-overview.md) propose deux modèles d’achat pour les ressources de calcul, de stockage et d’E/S : un modèle d’achat DTU et un modèle d’achat vCore (en préversion). Le tableau et le graphique suivants comparent ces deux modèles d’achat.

> [!IMPORTANT]
> Pour le modèle d’achat basé sur les DTU, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md).


|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure regroupant des ressources de calcul, de stockage et d’E/S. Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-what-is-a-dtu.md).|Idéal pour les clients qui souhaitent des options de ressources préconfigurées et simples.| 
|Modèle vCore|Ce modèle vous permet de mettre à l’échelle les ressources de calcul et les ressources de stockage indépendamment : jusqu’à 80 vCore, 4 To de stockage de données et 200 000 IOPS. Il permet également d’utiliser Azure Hybrid Benefit pour SQL Server afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![Modèle de prix](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>Modèle d’achat vCore (préversion)

Un vCore est l’UC logique qui permet de choisir parmi plusieurs générations de matériel. Le modèle d’achat vCore (en préversion) apporte flexibilité, contrôle et transparence pour la consommation des ressources. En outre, il permet de traduire facilement les exigences des charges de travail locales pour le cloud. Ce modèle permet de mettre à l’échelle le calcul, la mémoire et le stockage en fonction des besoins des charges de travail. Dans le modèle d’achat vCore (préversion), les clients peuvent choisir entre les niveaux de service Usage général et Critique pour l’entreprise (en préversion) pour les [bases de données uniques](sql-database-single-database-resources.md) et pour les [pools élastiques](sql-database-elastic-pool.md). 

Chaque niveau de service a son propre niveau de performance, la conception de sa haute disponibilité, son isolation des défaillances, ses types de stockage et sa plage d’E/S. Le client doit configurer séparément le stockage nécessaire et la période de rétention des sauvegardes. Lorsque vous utilisez le modèle vCore, les bases de données uniques et les pools élastiques permettent de réaliser jusqu’à 30 % d’économies avec [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Dans le modèle d’achat vCore (préversion), les clients paient pour :
- Le calcul (niveau de service + nombre de vCore + génération du matériel)*
- Le type et la quantité de stockage des journaux et des données 
- Le nombre d’E/S**
- Le stockage de sauvegarde (RA-GRS)** 

\* Dans la préversion publique initiale, les processeurs logiques Gen 4 sont basés sur des processeurs Intel E5-2673 v3 (Haswell) 2.4 GHz.

\*\* Dans la préversion, les sauvegardes et les E/S sont gratuites pendant 7 jours.

> [!IMPORTANT]
> Le calcul, les E/S, ainsi que le stockage des données et des journaux, sont facturés au niveau de chaque base de données ou au niveau du pool élastique. Le stockage des sauvegardes est facturé au niveau de chaque base de données. Pour plus d’informations sur les frais liés à Managed Instance, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md).

> [!IMPORTANT]
> Limitations de région : 
>
> Le modèle d’achat basé sur vCore (préversion) n’est pas encore disponible dans le sud-est de l’Australie. L’aperçu n’est pas disponible dans les régions suivantes : Europe de l’ouest, France-Centre, sud du Royaume-Uni et ouest du Royaume-Uni.
> 

## <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Choix du niveau de service et des ressources de calcul, de mémoire, de stockage et d’E/S

Le passage au modèle d’achat vCore (préversion) vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Si votre base de données ou pool élastique consomme plus de 300 DTU, le passage au modèle vCore peut vous permettre de réaliser des économies. Vous pouvez passer au modèle vCore à l’aide de l’API de votre choix ou du portail Azure, sans aucun temps d’arrêt. Le passage au modèle vCore n’est toutefois pas obligatoire. Si le modèle d’achat DTU répond à vos besoins métier et à vos besoins en performance, vous devez continuer de l’utiliser. Si vous décidez de passer du modèle DTU au modèle vCore, vous devez sélectionner le niveau de performance en suivant la règle de base suivante : chaque groupe de 100 DTU du niveau Standard nécessite au moins 1 vCore dans le niveau Usage général, et chaque groupe de 125 DTU du niveau Premium nécessite au moins 1 vCore Critique pour l'entreprise.

Le tableau suivant montre les différences entre ces deux niveaux :

||**Usage général**|**Critique pour l’entreprise**|
|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques.|Applications métier avec besoins en E/S élevés. Offre la meilleure résilience aux échecs en utilisant plusieurs répliques isolées.|
|Calcul|Entre 1 et 80 vCore, générations 4 et 5 |Entre 1 et 80 vCore, générations 4 et 5|
|Mémoire|7 Go par cœur |7 Go par cœur |
|Stockage|Stockage distant Premium, de 5 Go à 4 To|Stockage sur SSD local, de 5 Go à 4 To|
|Débit d’E/S (approximatif)|500 IOPS par vCore avec 7000 IOPS au maximum|5000 IOPS par cœur avec 200 000 IOPS au maximum|
|Disponibilité|1 réplica, sans échelle lecture|3 réplicas, 1 [échelle lecture](sql-database-read-scale-out.md), haute disponibilité redondante dans une zone|
|Sauvegardes|RA-GRS, 7 à 35 jours (7 jours par défaut)|RA-GRS, 7 à 35 jours (7 jours par défaut)*|
|En mémoire|N/A|Prise en charge|
|||

\* Dans la préversion, la période de rétention des sauvegardes n’est pas configurable et est fixée à 7 jours.

> [!IMPORTANT]
> Si vous avez besoin d’une capacité de calcul inférieure à 1 vCore, utilisez le modèle d’achat DTU.

Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels). Pour les pools élastiques, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées. 

## <a name="storage-considerations"></a>Considérations relatives au stockage

Tenez compte des éléments suivants :
- Le stockage alloué est utilisé par les fichiers de données (MDF) et les fichiers journaux (LDF).
- Chaque niveau de performance accepte une taille de base de données maximale, qui correspond, par défaut, à 32 Go.
- Lorsque vous configurez la taille de base de données nécessaire (c’est-à-dire, la taille des fichiers MDF), 30 % de stockage supplémentaire sont automatiquement ajoutés pour prendre en charge les fichiers LDF.
- Vous pouvez choisir n’importe quelle taille de base de données située entre 10 Go et la taille maximale prise en charge.
 - Pour le stockage Standard, augmentez ou diminuez la taille par incréments de 10 Go
 - Pour le stockage Premium, augmentez ou diminuez la taille par incréments de 250 Go
- Dans le niveau de service Usage général, `tempdb` utilise un disque SSD attaché et le coût de ce stockage est inclus dans le prix du modèle vCore.
- Dans le niveau de service Critique pour l’entreprise, `tempdb` partage le disque SSD attaché avec les fichiers MDF et LDF, et le coût du stockage tempDB est inclus dans le prix du modèle vCore.

> [!IMPORTANT]
> Le stockage total alloué aux fichiers MDF et LDF vous est facturé.

Pour surveiller la taille totale actuelle des fichiers MDF et LDF, utilisez [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Pour surveiller la taille actuelle de chaque fichier MDF et LDF, utilisez [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Du stockage de sauvegardes de base de données est alloué pour prendre en charge les fonctionnalités Limite de restauration dans le temps et Rétention à long terme de SQL Database. Ce stockage est alloué séparément pour chaque base de données. De plus, les fonctionnalités Limite de restauration dans le temps et Conservation à long terme sont, elles aussi, facturées séparément. 

- **Limite de restauration dans le temps** : les sauvegardes de bases de données sont automatiquement copiées vers le stockage RA-GRS. La taille de stockage augmente dynamiquement avec chaque nouvelle création de sauvegarde.  Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux copiés toutes les 5 minutes. La consommation du stockage dépend du taux de change de la base de données et de la période de rétention. Vous pouvez configurer une période de rétention distincte pour chaque base de données, allant de 7 à 35 jours. Un volume de stockage minimal correspondant à la taille des données est fourni sans frais supplémentaires. Pour la plupart des bases de données, cette quantité est suffisante pour stocker l’équivalent de 7 jours de sauvegardes.
- **Rétention à long terme** : SQL Database permet de configurer une rétention à long terme des sauvegardes complètes d’une durée de 10 ans. Si la stratégie de rétention à long terme est activée, ces sauvegardes sont stockées automatiquement dans le stockage RA-GRS. Toutefois, vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. Cette configuration définit la quantité de stockage utilisée pour les sauvegardes de rétention à long terme. Vous pouvez utiliser la calculatrice de prix LTR pour estimer le coût du stockage de rétention à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Dans le modèle d’achat vCore (préversion), vous pouvez échanger vos licences existantes avec des tarifs réduits sur SQL Database, en utilisant [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Cet avantage Azure vous permet d’utiliser vos licences SQL Server locales pour économiser jusqu’à 30 % sur Azure SQL Database, avec Software Assurance.

![Prix](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migration de bases de données uniques avec des liens de géoréplication

La migration du modèle DTU vers le modèle vCore (et inversement) est similaire à la mise à niveau (ou à la rétrogradation) des relations de géoréplication entre les bases de données Standard et Premium. Cela ne nécessite pas l’arrêt de la géoréplication, toutefois, l’utilisateur doit respecter les règles de séquencement. Lors d’une mise à niveau, vous devez mettre à niveau la base de données secondaire, avant de mettre à niveau la base de données primaire. Lors d’une rétrogradation, inversez l’ordre : rétrogradez d’abord la base de données primaire, puis la base de données secondaire. 

Lorsque vous utilisez la géoréplication entre deux pools élastiques, il est fortement recommandé de désigner un pool comme le pool principal et l’autre comme le pool secondaire. Dans ce cas, utilisez les mêmes recommandations pour la migration des pools élastiques.  Toutefois, il est techniquement possible qu’un pool élastique contienne à la fois la base de données primaire et la base de données secondaire. Dans ce cas, pour effectuer correctement la migration, vous devez désigner le pool ayant l’utilisation la plus élevée comme étant le pool principal, et suivre les règles de séquencement.  

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

## <a name="migration-of-failover-groups"></a>Migration des groupes de basculement 

La migration des groupes de basculement comprenant plusieurs bases de données nécessite que la base de données primaire et la base de données secondaire soient migrées séparément. Pendant ce processus, les mêmes recommandations et règles de séquencement s’appliquent. Une fois les bases de données converties au modèle vCore, le groupe de basculement reste actif, avec les mêmes paramètres de stratégie. 

## <a name="creation-of-a-geo-replication-secondary"></a>Création d’une base de données secondaire de géoréplication

Pour créer une base de données secondaire de géoréplication, vous devez utiliser le même niveau de service que la base de données primaire. Pour les bases de données avec un taux élevé de génération de journaux, il est fortement recommandé de créer la base de données secondaire avec le même niveau de performance que la base de données primaire. Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire, il est fortement conseillé que le paramètre `maxVCore` du pool corresponde au niveau de performance de la base de données primaire. Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire située dans un autre pool élastique, il est fortement conseillé d’attribuer la même valeur au paramètre `maxVCore` des deux pools.

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utilisation de la copie de base de données pour convertir une base de données DTU en base de données vCore

Vous pouvez copier n’importe quelle base de données avec niveau de performance DTU vers une base de données avec niveau de performance vCore, sans aucune restriction ni séquencement spécial, tant que le niveau de performance cible prend en charge la taille maximale de la base de données source. En effet, la copie de base de données crée un instantané des données dès que commence l’opération de copie, et elle n’effectue pas de synchronisation des données entre la source et la cible. 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles, consultez [Limites des ressources DTU SQL Database](sql-database-dtu-resource-limits.md) et [Limites des ressources vCore SQL Database](sql-database-vcore-resource-limits.md).
- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- En savoir plus sur [l’abonnement Azure et les limites, quotas et contraintes des services](../azure-subscription-service-limits.md)
