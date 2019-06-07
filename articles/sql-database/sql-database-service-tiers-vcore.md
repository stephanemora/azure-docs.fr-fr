---
title: Service Azure SQL Database - vCore | Microsoft Docs
description: Le modèle d’achat vCore vous permet de mettre à l’échelle des ressources de calcul et de stockage indépendamment, égaler les performances en local et l’optimisation des prix.
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
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693331"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Choisissez parmi les niveaux de service de vCore et migrer depuis les niveaux de service DTU

Le noyau virtuel (vCore)-basée sur modèle d’achat vous permet de mettre à l’échelle des ressources de calcul et de stockage indépendamment, égaler les performances en local et l’optimisation des prix. Il vous permet également de choisir la génération de matériel :

- **Gen4**: Jusqu'à 24 processeurs logiques basés sur Intel E5-2673 v3 (Haswell) 2,4 GHz, vCore = 1 PP (cœur physique), 7 Go par cœur, de disque SSD attaché
- **Gen5**: Jusqu'à 80 processeurs logiques basés sur Intel E5-2673 v4 (Broadwell) 2.3 GHz processeurs, vCore = 1 LP (hyper-thread), 5.1 Go par cœur, fast eNVM SSD

Le matériel Gen4 offre bien plus de mémoire par vCore. Toutefois, le matériel Gen5 vous permet de monter en puissance les ressources de calcul de façon plus importante.

> [!NOTE]
> Pour plus d’informations sur les niveaux de service basé sur DTU, consultez [niveaux pour le modèle d’achat DTU de Service](sql-database-service-tiers-dtu.md). Pour plus d’informations sur les différences entre les niveaux de service pour basé sur les DTU et les modèles d’achat vCore, consultez [modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Caractéristiques de niveau de service

Le modèle d’achat vCore fournit trois niveaux de service : usage général, hyperscale et critique pour l’entreprise. Ces niveaux de service est différenciées par une plage de tailles de calcul, les conceptions de haute disponibilité, les méthodes de localisation des pannes, les types et les tailles de stockage et les plages d’e/s.

Vous devez configurer séparément le stockage nécessaire et la période de rétention des sauvegardes. Pour définir la période de rétention de sauvegarde, ouvrez le portail Azure, accédez au serveur (pas la base de données), puis accédez à **gérer les sauvegardes** > **configurer la stratégie**  >   **Point de temps restaurer la Configuration de** > **7 à 35 jours**.

Le tableau suivant explique les différences entre les trois niveaux :

||**Usage général**|**Critique pour l’entreprise**|**Hyperscale**|
|---|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Calcul orientées budget, équilibrée et scalable d’offres et options de stockage.|Applications métier avec des exigences élevées en termes d’e/s. Offre la meilleure résilience aux défaillances à l’aide de plusieurs répliques isolées.|La plupart des entreprises les charges de travail avec un stockage hautement évolutif et exigences de l’échelle de lecture.|
|Calcul|**Mise en service de calcul**:<br/>Gen4 : 1 à 24 VCORE<br/>Gen5 : 2 à 80 VCORE<br/>**Calcul sans serveur**:<br/>Gen5 : 0,5 - 4 vCores|**Mise en service de calcul**:<br/>Gen4 : 1 à 24 VCORE<br/>Gen5 : 2 à 80 VCORE|**Mise en service de calcul**:<br/>Gen4 : 1 à 24 VCORE<br/>Gen5 : 2 à 80 VCORE|
|Mémoire|**Mise en service de calcul**:<br/>Gen4 : 7 Go par vCore<br/>Gen5 : 5,1 Go par vCore<br/>**Calcul sans serveur**:<br/>Gen5 : 3 Go par vCore|**Mise en service de calcul**:<br/>Gen4 : 7 Go par vCore<br/>Gen5 : 5,1 Go par vCore |**Mise en service de calcul**:<br/>Gen4 : 7 Go par vCore<br/>Gen5 : 5,1 Go par vCore|
|Stockage|Utilise le stockage à distance.<br/>**Base de données unique mis en service de calcul**:<br/>5 Go - 4 To<br/>**Calcul sans serveur de base de données unique**:<br/>5 GO À 1 TO<br/>**Instance managée**: 32 Go - 8 To |Utilise le stockage SSD local.<br/>**Base de données unique mis en service de calcul**:<br/>5 Go - 4 To<br/>**Instance managée**:<br/>32 Go - 4 To |Croissance flexible de stockage en fonction des besoins. Prend en charge jusqu'à 100 To de stockage. Utilise le stockage SSD local pour le cache de pool de mémoires tampons local et stockage de données local. Utilise le stockage à distance Azure comme magasin de données à long terme finale. |
|Débit d’e/s (approximatif)|**Base de données unique**: 500 e/s par vCore avec le nombre maximal de 7000 IOPS.<br/>**Instance managée**: Dépend de [taille du fichier](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS par cœur avec 200 000 IOPS au maximum|Très grande échelle est une architecture à plusieurs niveaux avec la mise en cache à plusieurs niveaux. Effectives d’IOPs dépendra de la charge de travail.|
|Disponibilité|1 réplica, aucun réplica avec échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](sql-database-read-scale-out.md),<br/>redondant haute disponibilité (HA)|1 réplica en lecture-écriture, ainsi que de 0 à 4 [les réplicas en lecture à l’échelle](sql-database-read-scale-out.md)|
|Sauvegardes|[Le stockage géo-redondant avec accès en lecture (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|Sauvegardes de captures instantanées dans le stockage Azure à distance. Les restaurations utilisent ces instantanés pour une récupération rapide. Les sauvegardes sont instantanés et performances d’e/s de calcul n’ont aucun impact. Les restaurations sont rapides et ne sont pas une opération de données de taille (prise en minutes plutôt que heures ou jours).|
|En mémoire|Non pris en charge|Pris en charge|Non pris en charge|
|||

> [!NOTE]
> Vous pouvez obtenir une base de données SQL Azure gratuit au niveau de service de base conjointement avec un compte gratuit Azure. Pour plus d’informations, consultez [créer une base de données cloud managée avec votre compte gratuit Azure](https://azure.microsoft.com/free/services/sql-database/).

- Pour plus d’informations sur les limites de ressources vCore, consultez [limites de ressources vCore d’une base de données](sql-database-vcore-resource-limits-single-databases.md) et [vCore des limites de ressources dans une instance gérée](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Pour plus d’informations sur les niveaux de service critique d’entreprise à usage général, consultez [niveaux de service usage général et critique pour l’entreprise](sql-database-service-tiers-general-purpose-business-critical.md).
- Pour plus d’informations sur le niveau de service de très grande échelle dans le modèle d’achat vCore, consultez [niveau de service Hyperscale](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Dans le niveau calcul provisionné du modèle d’achat vCore, vous pouvez échanger vos licences existantes avec des tarifs réduits sur SQL Database à l’aide de [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Cet avantage Azure vous permet d’enregistrer jusqu'à 30 % sur la base de données SQL Azure à l’aide de vos licences de SQL Server locales avec Software Assurance.

![Prix](./media/sql-database-service-tiers/pricing.png)

Avec Azure Hybrid Benefit, vous pouvez choisir de payer uniquement pour l’infrastructure Azure sous-jacente à l’aide de votre licence de SQL Server existante pour le moteur de base de données SQL lui-même (Base de calcul de tarification), ou vous pouvez payer pour l’infrastructure sous-jacente et le serveur SQL Server licence (licence incluse de tarification).

Vous pouvez choisir ou modifier votre modèle de licence à l’aide du portail Azure ou en utilisant l’une des API suivantes :

- Pour définir ou mettre à jour le type de licence à l’aide de PowerShell :

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Pour définir ou mettre à jour le type de licence à l’aide de l’interface CLI :

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Pour définir ou mettre à jour le type de licence à l’aide de l’API REST :

  - [Bases de données : Create ou Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bases de données - Mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Instances managées - Créer ou Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Instances managées - Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrer à partir du modèle basé sur DTU vers le modèle basé sur vCore

### <a name="migrate-a-database"></a>Migrer une base de données

Migration d’une base de données à partir du modèle d’achat DTU vers le modèle d’achat vCore est similaire à la mise à niveau ou rétrogradation entre les niveaux de service standard et premium dans le modèle d’achat DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrer des bases de données avec des liens de géo-réplication

Migration à partir du modèle basé sur DTU pour le modèle d’achat vCore est similaire à la mise à niveau ou rétrogradation les relations de géo-réplication entre les bases de données dans les niveaux de service standard et premium. Pendant la migration, vous n’êtes pas obligé d’arrêter la géo-réplication, mais vous devez suivre ces règles de séquencement :

- Lors d’une mise à niveau, vous devez mettre à niveau la base de données secondaire, avant de mettre à niveau la base de données primaire.
- Lors d’une rétrogradation, inversez l’ordre : rétrogradez d’abord la base de données primaire, puis la base de données secondaire.

Lorsque vous utilisez la géo-réplication entre deux pools élastiques, nous vous recommandons de désigner un pool comme le réplica principal et l’autre en tant que la base de données secondaire. Dans ce cas, lorsque vous effectuez une migration des pools élastiques, vous devez utiliser les mêmes recommandations de séquencement. Toutefois, si vous avez des pools élastiques qui contiennent des bases de données primaires et secondaire, traiter le pool avec l’utilisation de la plus élevée que le principal et suivez les règles de séquencement en conséquence.  

Le tableau suivant fournit des conseils pour les scénarios de migration spécifique :

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

\* Chaque 100 dtu dans le niveau standard nécessite au moins 1 vCore, et chaque 125 dtu du niveau premium nécessite au moins 1 vCore.

### <a name="migrate-failover-groups"></a>Migrer des groupes de basculement

La migration des groupes de basculement comprenant plusieurs bases de données nécessite que la base de données primaire et la base de données secondaire soient migrées séparément. Pendant ce processus, les mêmes recommandations et règles de séquencement s’appliquent. Une fois les bases de données sont converties en le modèle d’achat vCore, le groupe de basculement reste en vigueur avec les mêmes paramètres de stratégie.

### <a name="create-a-geo-replication-secondary-database"></a>Créer une base de données secondaire géo-réplication

Vous pouvez créer une base de données secondaire géo-réplication (géo-secondaire) uniquement à l’aide du même niveau de service que celui utilisé pour la base de données primaire. Pour les bases de données avec un taux élevé de génération de journaux, nous vous recommandons de création de la géo-secondaire avec la même taille de calcul en tant que le réplica principal.

Si vous créez une base de données géo-secondaire dans le pool élastique pour une base de données primaire unique, vérifiez que le `maxVCore` paramètre pour le pool correspond à la taille de calcul de base de données primaire. Si vous créez une zone géographique secondaire pour un serveur principal dans un autre pool élastique, nous recommandons que les pools ont les mêmes `maxVCore` paramètres.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Copie de base de données permet de convertir une base de données basée sur les DTU pour une base de données basé sur vCore

Vous pouvez copier n’importe quelle base de données avec une taille de calcul DTU vers une base de données avec une taille de calcul vCore, sans aucune restriction ni séquencement spécial, tant que la taille de calcul cible prend en charge la taille maximale de la base de données source. La copie de base de données crée un instantané des données à l’heure de début de l’opération de copie et de ne synchronise des données entre la source et la cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour les tailles de calcul spécifiques et les options de taille de stockage disponibles pour les bases de données uniques, consultez [limites de ressources VCORE de base de données SQL pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).
- Pour les tailles de calcul spécifiques et les options de taille de stockage disponibles pour les pools élastiques, consultez [limites de ressources VCORE de base de données SQL pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
