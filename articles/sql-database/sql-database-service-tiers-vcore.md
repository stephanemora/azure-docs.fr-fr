---
title: Service Azure SQL Database - vCore | Microsoft Docs
description: Le modèle d’achat vCore vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 06/26/2019
ms.openlocfilehash: e5af3803ebb4cb0a88a082d3c85d0df68da8d1b8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566614"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Choisissez parmi les niveaux de service vCore et effectuez la migration depuis les niveaux de service DTU

Le modèle d’achat vCore (cœurs virtuels) vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Il vous permet également de choisir la génération du matériel :

- **Gen4** : jusqu’à 24 processeurs logiques basés sur le processeur Intel E5-2673 v3 (Haswell) de 2,4 GHz, vCore = 1 PP (cœur physique), 7 Go par cœur, disque SSD attaché
- **Gen5** : jusqu’à 80 processeurs logiques basés sur le processeur Intel E5-2673 v4 (Broadwell) de 2,3 GHz, vCore = 1 LP (hyper-thread), 5.1 Go par cœur, disque SSD fast eNVM

Le matériel Gen4 offre bien plus de mémoire par vCore. Toutefois, le matériel Gen5 vous permet de monter en puissance les ressources de calcul de façon plus importante.

> [!IMPORTANT]
> Les nouvelles bases de données Gen4 ne sont plus prises en charge dans la région AustraliaEast.
> [!NOTE]
> Pour plus d’informations sur les niveaux de service DTU, consultez [Niveaux de service pour le modèle d’achat DTU](sql-database-service-tiers-dtu.md). Pour plus d’informations sur les différences entre les niveaux de service pour modèles d’achat DTU et vCore, consultez [Modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Caractéristiques des niveaux de service

Le modèle d’achat vCore fournit trois niveaux de service : usage général, hyperscale et critique pour l’entreprise. Ces niveaux de service se distinguent par leur taille de calcul, leur conception de haute disponibilité, leurs méthodes d’isolation des défaillances, leurs types et tailles de stockage, et leurs plages d’E/S.

Vous devez configurer séparément le stockage nécessaire et la période de rétention des sauvegardes. Pour définir la période de rétention de sauvegarde, ouvrez le Portail Azure, accédez au serveur (pas à la base de données), puis sélectionnez **Gérer les sauvegardes** > **Configurer la stratégie** > **Configuration de la limite de restauration dans le temps** > **7 à 35 jours**.

Le tableau suivant explique les différences entre les trois niveaux :

||**Usage général**|**Critique pour l’entreprise**|**Hyperscale**|
|---|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques.|Applications métier avec besoins en E/S élevés. Offre la meilleure résilience aux défaillances grâce à l’utilisation de plusieurs réplicas isolés.|La plupart des charges de travail métier avec des exigences de stockage et d’échelle lecture à haute scalabilité.|
|Calcul|**Calcul provisionné** :<br/>Gen4 : 1 à 24 cœurs virtuels<br/>Gen5 : 2 à 80 cœurs virtuels<br/>**Calcul serverless** :<br/>Gen5 : 0,5 à 4 cœurs virtuels|**Calcul provisionné** :<br/>Gen4 : 1 à 24 cœurs virtuels<br/>Gen5 : 2 à 80 cœurs virtuels|**Calcul provisionné** :<br/>Gen4 : 1 à 24 cœurs virtuels<br/>Gen5 : 2 à 80 cœurs virtuels|
|Mémoire|**Calcul provisionné** :<br/>Gen4 : 7 Go par vCore<br/>Gen5 : 5,1 Go par vCore<br/>**Calcul serverless** :<br/>Gen5 : 3 Go par vCore|**Calcul provisionné** :<br/>Gen4 : 7 Go par vCore<br/>Gen5 : 5,1 Go par vCore |**Calcul provisionné** :<br/>Gen4 : 7 Go par vCore<br/>Gen5 : 5,1 Go par vCore|
|Stockage|Utilise le stockage à distance.<br/>**Calcul provisionné par une base de données unique** :<br/>5 Go - 4 To<br/>**Calcul serverless avec base de données unique** :<br/>5 Go - 1 To<br/>**Instance managée** : 32 Go - 8 To |Utilise le stockage SSD local.<br/>**Calcul provisionné par une base de données unique** :<br/>5 Go - 4 To<br/>**Instance managée** :<br/>32 Go - 4 To |Croissance automatique et flexible du stockage en fonction des besoins. Prend en charge jusqu’à 100 To de stockage. Utilise le stockage SSD local pour le cache du pool de mémoires tampons local et le stockage de données local. Utilise le stockage distant Azure comme banque de données finale à long terme. |
|Débit d’E/S (approximatif)|**Base de données unique** : 500 IOPS par vCore avec un maximum de 7 000 IOPS<br/>**Instance managée** : Dépend de la [taille de fichier](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS par cœur avec 200 000 IOPS au maximum|L’architecture hyperscale est une architecture à plusieurs niveaux avec une mise en cache sur plusieurs niveaux. L’efficacité des IOPS dépend de la charge de travail.|
|Disponibilité|1 réplica, réplicas sans échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](sql-database-read-scale-out.md),<br/>haute disponibilité (HA) redondante interzone|1 réplica en lecture-écriture, plus 0 à 4 [réplicas avec échelle lecture](sql-database-read-scale-out.md)|
|Sauvegardes|[Stockage géo-redondant avec accès en lecture (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|Sauvegardes basées sur des instantanés dans le stockage distant Azure. Les restaurations utilisent ces instantanés pour une récupération rapide. Les sauvegardes sont instantanées et n’ont aucun impact sur les performances d’E/S de calcul. Les restaurations sont rapides et ne sont pas des opérations à l’échelle des données (elles durent quelques minutes plutôt que quelques heures ou jours).|
|En mémoire|Non pris en charge|Pris en charge|Non pris en charge|
|||

> [!NOTE]
> Vous pouvez obtenir une base de données Azure SQL Database gratuite au niveau de service De base avec un compte Azure gratuit. Pour plus d’informations, rendez-vous sur la page [Créer une base de données cloud managée avec votre compte gratuit Azure](https://azure.microsoft.com/free/services/sql-database/).

- Pour plus d’informations à ce sujet, consultez [Limites de ressources vCore dans une base de données unique](sql-database-vcore-resource-limits-single-databases.md) et [Limites de ressources vCore dans une instance managée](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Pour plus d’informations sur les niveaux de service Usage général et Critique pour l’entreprise, consultez [Niveaux de service Usage général et Critique pour l’entreprise](sql-database-service-tiers-general-purpose-business-critical.md).
- Pour plus d’informations sur le niveau de service Hyperscale dans le modèle d’achat vCore, consultez [Niveau de service Hyperscale](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Dans le niveau de calcul provisionné du modèle d’achat vCore, vous pouvez échanger vos licences existantes avec des tarifs réduits sur SQL Database, en utilisant [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Cet avantage Azure vous permet d’économiser jusqu’à 30 % sur Azure SQL Database en utilisant vos licences SQL Server locales avec la Software Assurance.

![Prix](./media/sql-database-service-tiers/pricing.png)

Avec Azure Hybrid Benefit, vous pouvez choisir de payer uniquement pour l’infrastructure Azure sous-jacente en utilisant votre licence SQL Server existante pour le moteur de base de données SQL lui-même (tarification de calcul de base) ou de payer à la fois pour l’infrastructure sous-jacente et pour la licence SQL Server (tarification avec licence incluse).

Vous pouvez choisir ou modifier votre modèle de licence en utilisant le Portail Azure ou l’une des API suivantes :

- Pour définir ou mettre à jour le type de licence à l’aide de PowerShell :

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Pour définir ou mettre à jour le type de licence à l’aide d’Azure CLI :

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Pour définir ou mettre à jour le type de licence à l’aide de l’API REST :

  - [Bases de données : Create ou Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bases de données - Mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Instances managées - Créer ou Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Instances managées - Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Effectuer une migration à partir du modèle DTU vers le modèle vCore

### <a name="migrate-a-database"></a>Migrer une base de données

La migration d’une base de données du modèle d’achat DTU vers le modèle d’achat vCore est similaire à la mise à niveau ou au passage à une version antérieure entre les niveaux de service standard et premium dans le modèle d’achat DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrer des bases de données avec des liens de géoréplication

La migration du modèle DTU vers le modèle vCore est similaire à la mise à niveau (ou à la rétrogradation) des relations de géoréplication entre les bases de données dans les niveaux de service standard et premium. Pendant la migration, vous n’êtes pas obligé d’arrêter la géoréplication, mais vous devez suivre ces règles de séquencement :

- Lors d’une mise à niveau, vous devez mettre à niveau la base de données secondaire, avant de mettre à niveau la base de données primaire.
- Lors d’une rétrogradation, inversez l’ordre : rétrogradez d’abord la base de données primaire, puis la base de données secondaire.

Lorsque vous utilisez la géoréplication entre deux pools élastiques, nous vous recommandons de désigner un pool comme le pool principal et l’autre comme le pool secondaire. Dans ce cas, lorsque vous effectuez une migration de pools élastiques, vous devez utiliser les mêmes recommandations de séquencement. Toutefois, si vous avez des pools élastiques qui contiennent des bases de données primaires et secondaire, traitez le pool le plus utilisé comme pool principal et suivez les règles de séquencement en conséquence.  

Le tableau suivant fournit des conseils pour certains scénarios de migration :

|Niveau de service actuel|Niveau de service cible|Type de migration|Actions utilisateur|
|---|---|---|---|
|standard|Usage général|Latéral|Peut effectuer la migration dans n’importe quel ordre, mais doit garantir un redimensionnement vCore adapté*|
|Premium|Critique pour l'entreprise|Latéral|Peut effectuer la migration dans n’importe quel ordre, mais doit garantir un redimensionnement vCore adapté*|
|standard|Critique pour l'entreprise|Mise à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
|Critique pour l'entreprise|standard|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Premium|Usage général|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Usage général|Premium|Mise à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
|Critique pour l'entreprise|Usage général|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Usage général|Critique pour l'entreprise|Mise à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
||||

\* Chaque ensemble de 100 unités de transaction de base de données du niveau standard nécessite au moins 1 cœur virtuel, et chaque ensemble de 125 DTU du niveau premium nécessite au moins 1 cœur virtuel.

### <a name="migrate-failover-groups"></a>Migrer des groupes de basculement

La migration des groupes de basculement comprenant plusieurs bases de données nécessite que la base de données primaire et la base de données secondaire soient migrées séparément. Pendant ce processus, les mêmes recommandations et règles de séquencement s’appliquent. Une fois les bases de données converties au modèle d’achat vCore, le groupe de basculement reste actif, avec les mêmes paramètres de stratégie.

### <a name="create-a-geo-replication-secondary-database"></a>Créer une base de données secondaire de géoréplication

Vous pouvez créer une base de données secondaire géoréplication (réplica géosecondaire) uniquement dans le même niveau de service que celui utilisé pour la base de données primaire. Pour les bases de données avec un taux élevé de génération de journaux, nous vous recommandons de créer le réplica géosecondaire avec la même taille de calcul que le réplica principal.

Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire unique, le paramètre `maxVCore` du pool doit correspondre à la taille de calcul de la base de données primaire. Si vous créez une base de données secondaire de géoréplication pour une base de données primaire située dans un autre pool élastique, nous vous recommandons d’attribuer la même valeur au paramètre `maxVCore` des deux pools.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utiliser la copie de base de données pour convertir une base de données DTU en base de données vCore

Vous pouvez copier n’importe quelle base de données avec une taille de calcul DTU vers une base de données avec une taille de calcul vCore, sans aucune restriction ni séquencement spécial, tant que la taille de calcul cible prend en charge la taille maximale de la base de données source. La copie de base de données crée un instantané des données dès que commence l’opération de copie, et elle ne synchrone pas les données entre la source et la cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les pools élastiques, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
