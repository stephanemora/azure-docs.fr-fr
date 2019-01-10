---
title: Service Azure SQL Database - vCore | Microsoft Docs
description: Le modèle d’achat vCore vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 01/02/2019
ms.openlocfilehash: f756f043a7ab3c9086b21b8bdb88a5a6a7ed60df
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001598"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>Niveaux de service vCore, Azure Hybrid Benefit et migration

Le modèle d’achat vCore vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Il vous permet également de choisir la génération de matériel :

- Gen 4 : jusqu'à 24 processeurs logiques basés sur le processeur Intel E5-2673 v3 (Haswell) de 2,4 GHz, vCore = 1 PP (cœur physique), 7 Go par cœur, disque SSD attaché
- Gen 5 : jusqu’à 80 processeurs logiques basés sur le processeur Intel E5-2673 v4 (Broadwell) de 2,3 GHz, vCore = 1 LP (hyper-thread), 5.1. Go par cœur, disque SSD fast eNVM

Le modèle vCore vous permet également d’utiliser [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) afin de réduire les coûts.

> [!NOTE]
> Pour plus d’informations sur les niveaux de service basés sur DTU, voir [Niveaux de service basés sur DTU](sql-database-service-tiers-dtu.md). Pour plus d’informations sur ce qui différencie les niveaux de service basés sur des unités DTU et les niveaux de service basés sur vCore, voir [Modèles d’achat d’Azure SQL Database](sql-database-service-tiers.md).

## <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

Le modèle vCore fournit trois niveaux de service, Usage général et Hyperscale et Critique pour l’entreprise. Chaque niveau de service se distingue par sa taille de calcul, sa conception de haute disponibilité, son isolation des défaillances, ses types et tailles de stockage, et sa plage d’E/S. Vous devez configurer séparément le stockage nécessaire et la période de rétention des sauvegardes. Dans le portail Azure, accédez à Serveur (pas à la base de données) > Managed Backups (Sauvegardes gérées) > Configurer la stratégie > Configuration de la limite de restauration dans le temps > 7 - 35 jours.

Le tableau suivant montre les différences entre les trois niveaux :

||**Usage général**|**Critique pour l’entreprise**|**Hyperscale (préversion)**|
|---|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques.|Applications métier avec besoins en E/S élevés. Offre la meilleure résilience aux échecs en utilisant plusieurs répliques isolées.|La plupart des charges de travail métier avec des exigences de stockage et d’échelle lecture à haute scalabilité|
|Calcul|Gen4 : de 1 à 24 vCore<br/>Gen5 : de 1 à 80 vCore|Gen4 : de 1 à 24 vCore<br/>Gen5 : de 1 à 80 vCore|Gen4 : de 1 à 24 vCore<br/>Gen5 : de 1 à 80 vCore|
|Mémoire|Gen4 : 7 Go par cœur<br>Gen5 : 5,1 Go par cœur | Gen4 : 7 Go par cœur<br>Gen5 : 5,1 Go par cœur |Gen4 : 7 Go par cœur<br>Gen5 : 5,1 Go par cœur|
|Stockage|Utilise le [Stockage distant Premium](../virtual-machines/windows/premium-storage.md) :<br/>Base de données unique : 5 Go - 4 To<br/>instance managée : 32 Go - 8 To |Utilise le stockage SSD local :<br/>Base de données unique : 5 Go - 4 To<br/>instance managée : 32 Go - 4 To |Croissance automatique et flexible du stockage en fonction des besoins. Prend en charge jusqu’à 100 To de stockage et au-delà. Stockage SSD local pour le cache du pool de mémoires tampons local et le stockage de données local. Stockage distant Azure comme magasin de données final à long terme. |
|Débit d’E/S (approximatif)|Base de données unique : 500 IOPS par vCore avec 7000 IOPS au maximum</br>instance managée : dépend de la [taille de fichier](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS par cœur avec 200 000 IOPS au maximum|TBD|
|Disponibilité|1 réplica, sans échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](sql-database-read-scale-out.md),<br/>Haute disponibilité redondante dans une zone|?|
|Sauvegardes|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|Sauvegarde basée sur des instantanés dans le stockage distant Azur. Les restaurations utilisent ces instantanés pour une récupération rapide. Les sauvegardes sont instantanées et n’ont aucun impact sur les performances d’E/S du calcul. Les restaurations sont très rapides (elles durent quelques minutes plutôt que quelques heures ou jours).|
|En mémoire|Non pris en charge|Pris en charge|Non pris en charge|
|||

> [!NOTE]
> Vous pouvez obtenir une base de données Azure SQL Database gratuite au niveau de service De base avec un compte Azure gratuit pour explorer Azure. Pour plus d’informations, rendez-vous sur la page [Créer une base de données cloud managée avec votre compte gratuit Azure](https://azure.microsoft.com/free/services/sql-database/).

- Pour plus d’informations, consultez [Limites de ressources vCore dans une base de données unique](sql-database-vcore-resource-limits-single-databases.md) et [Limites de ressources vCore dans une instance managée](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Pour plus d’informations sur les niveaux de service Usage général et Critique pour l’entreprise, consultez l’article [General Purpose and Business Critical service tiers](sql-database-service-tiers-general-purpose-business-critical.md) (Niveaux de service Usage général et Critique pour l’entreprise).
- Pour plus d’informations sur le niveau de service Hyperscale dans le modèle d’achat basé sur le nombre de cœurs virtuels, consultez [Niveau de service Hyperscale](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Si vous avez besoin d’une capacité de calcul inférieure à 1 vCore, utilisez le modèle d’achat DTU.

Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Dans le modèle d’achat basé sur le nombre de vCores, vous pouvez échanger vos licences existantes avec des tarifs réduits sur SQL Database, en utilisant [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Cet avantage Azure vous permet d’utiliser vos licences SQL Server locales pour économiser jusqu’à 30 % sur Azure SQL Database, avec Software Assurance.

![Prix](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migration du modèle DTU vers le modèle vCore

### <a name="migration-of-a-database"></a>Migration d’une base de données

La migration d’une base de données du modèle d’achat DTU vers le modèle d’achat basé sur le nombre de vCores est similaire à la mise à niveau ou au passage à une version antérieure entre les versions Standard et Premium des bases de données dans le modèle d’achat DTU.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migration de bases de données avec des liens de géoréplication

La migration du modèle DTU vers le modèle vCore est similaire à la mise à niveau (ou à la rétrogradation) des relations de géoréplication entre les bases de données Standard et Premium. Cela ne nécessite pas l’arrêt de la géoréplication, toutefois, l’utilisateur doit respecter les règles de séquencement. Lors d’une mise à niveau, vous devez mettre à niveau la base de données secondaire, avant de mettre à niveau la base de données primaire. Lors d’une rétrogradation, inversez l’ordre : rétrogradez d’abord la base de données primaire, puis la base de données secondaire.

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

Pour créer une base de données secondaire de géoréplication, vous devez utiliser le même niveau de service que la base de données primaire. Pour les bases de données avec un taux élevé de génération de journaux, il est recommandé de créer la base de données secondaire avec la même taille de calcul que la base de données primaire. Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire unique, le paramètre `maxVCore` du pool doit correspondre à la taille de calcul de la base de données primaire. Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire située dans un autre pool élastique, il est conseillé d’attribuer la même valeur au paramètre `maxVCore` des deux pools.

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utilisation de la copie de base de données pour convertir une base de données DTU en base de données vCore

Vous pouvez copier n’importe quelle base de données avec une taille de calcul DTU vers une base de données avec une taille de calcul vCore, sans aucune restriction ni séquencement spécial, tant que la taille de calcul cible prend en charge la taille maximale de la base de données source. La copie de base de données crée un instantané des données dès que commence l’opération de copie, et elle n’effectue pas de synchronisation des données entre la source et la cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les pools élastiques, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
