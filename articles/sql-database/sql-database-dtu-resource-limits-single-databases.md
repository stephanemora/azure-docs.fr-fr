---
title: Limites de ressources DTU de bases de données uniques dans Azure SQL Database | Microsoft Docs
description: Cette page décrit certaines limites de ressources DTU courantes pour des bases de données uniques dans Azure SQL Database.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: carlrab
ms.openlocfilehash: 2d6660e1064959f2d04424ae1c3e9bc668231c92
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131319"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU 

Cet article détaille les limites de ressources des pools élastiques Azure SQL Database suivant le modèle d’achat DTU.

Pour connaître les limites de ressources des pools élastiques du modèle d’achat DTU, consultez [Limites des ressources DTU : pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md). Pour voir les limites de ressources par vCore, consultez [Limites de ressources par vCore : bases de données uniques](sql-database-vcore-resource-limits-single-databases.md) et [Limites de ressources par vCore : pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Base de données unique : tailles de stockage et niveaux de performance

Pour les bases de données uniques, les tableaux suivants indiquent les ressources disponibles pour une base de données unique à chaque niveau de performance et de service. Vous pouvez définir le niveau de service, le niveau de performance et la quantité de stockage pour une base de données unique à l’aide du [portail Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), de [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), d’[Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases) ou de l’[API REST](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Niveau de service De base
| **Niveau de performances** | **De base** |
| :--- | --: |
| DTU max | 5. |
| Espace de stockage inclus (Go) | 2 |
| Choix de stockage maximum (Go) | 2 |
| Stockage In-Memory OLTP maximal (Go) |N/A |
| Nombre maximal d’ouvriers simultanés (demandes) | 30 |
| Nombre maximal de sessions simultanées | 300 |
|||

### <a name="standard-service-tier"></a>Niveau de service Standard
| **Niveau de performances** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| DTU max | 10 | 20 | 50 | 100 |
| Espace de stockage inclus (Go) | 250 | 250 | 250 | 250 |
| Choix de stockage maximum (Go) | 250 | 250 | 250 | 250, 500, 750, 1 024 |
| Stockage In-Memory OLTP maximal (Go) | N/A | N/A | N/A | N/A |
| Nombre maximal d’ouvriers simultanés (demandes)| 60 | 90 | 120 | 200 |
| Nombre maximal de sessions simultanées |600 | 900 | 1 200 | 2 400 |
||||||

### <a name="standard-service-tier-continued"></a>Niveau de service Standard (suite)
| **Niveau de performances** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| DTU max | 200 | 400 | 800 | 1 600 | 3000 |
| Espace de stockage inclus (Go) | 250 | 250 | 250 | 250 | 250 |
| Choix de stockage maximum (Go) | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 |
| Stockage In-Memory OLTP maximal (Go) | N/A | N/A | N/A | N/A |N/A |
| Nombre maximal d’ouvriers simultanés (demandes)| 400 | 800 | 1 600 | 3200 |6000 |
| Nombre maximal de sessions simultanées |4 800 | 9 600 | 19 200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Niveau de service Premium 
| **Niveau de performances** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| DTU max | 125 | 250 | 500 | 1 000 | 1 750 | 4000 |
| Espace de stockage inclus (Go) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Choix de stockage maximum (Go) | 500, 750, 1 024 | 500, 750, 1 024 | 500, 750, 1 024 | 500, 750, 1 024 | 4096 | 4096 |
| Stockage In-Memory OLTP maximal (Go) | 1 | 2 | 4 | 8 | 14 | 32 |
| Nombre maximal d’ouvriers simultanés (demandes)| 200 | 400 | 800 | 1 600 | 2 400 | 6 400 |
| Nombre maximal de sessions simultanées | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Le niveau Premium offre plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : USA Centre-Ouest, Chine Est, USDoDCentral, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, Allemagne - Nord-Est, US Gov Iowa, Chine Nord. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Base de données unique : modifier la taille de stockage

- Le prix des DTU pour une base de données unique inclut une certaine quantité de stockage sans coût supplémentaire. Un espace de stockage en plus du volume inclus peut être approvisionné pour un coût supplémentaire jusqu’à la limite de taille par incréments de 250 Go jusqu’à 1 To, puis par incréments de 256 Go au-delà de 1 To. Pour les quantités de stockage et limites de taille maximale incluses, consultez [Base de données unique : tailles de stockage et niveaux de performance](#single-database-storage-sizes-and-performance-levels).
- Vous pouvez configurer du stockage supplémentaire pour une base de données en augmentant sa taille maximale à l’aide du [portail Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), de [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az_sql_db_update) ou de [l’API REST](/rest/api/sql/databases/update).
- Le prix de l’espace de stockage supplémentaire pour une base de données unique est égal au volume de stockage supplémentaire multiplié par le prix unitaire du stockage supplémentaire pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Base de données unique : modifier les DTU

Après la sélection initiale d’un niveau de service, d’un niveau de performance et d’une quantité de stockage, vous pouvez faire évoluer une base de données vers le haut ou vers le bas de façon dynamique en fonction de l’expérience réelle à l’aide du [portail Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), de [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az_sql_db_update) ou de [l’API REST](/rest/api/sql/databases/update). 

La vidéo suivante montre la modification dynamique du niveau de performance pour augmenter les DTU disponibles pour une base de données.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Le délai de basculement peut varier, mais il est inférieur à 30 secondes dans 99 % des cas. Ce délai peut se révéler supérieur, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées. 

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de six heures. Le changement des niveaux de performances d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de trois heures.

> [!TIP]
> Pour surveiller les opérations en cours, consultez la [gestion des opérations à l’aide de l’API REST SQL](/rest/api/sql/Operations/List), la [gestion des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), la [surveillance des opérations à l’aide T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), ainsi que ces deux commandes PowerShell : [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) et [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Si vous effectuez la mise à niveau vers un niveau de service ou de performance supérieur, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande (maxsize).
* Pour pouvoir passer à une version antérieure, l’espace utilisé par la base de données doit être inférieur à la taille et au niveau de performance autorisés par le niveau de service voulu. 
* Lorsque vous rétrogradez du niveau **Premium** vers le niveau **Standard**, un coût de stockage supplémentaire s’applique si les (1) la taille maximale de la base de données est prise en charge dans le niveau de performance cible et (2) la taille maximale dépasse la quantité de stockage incluse dans le niveau de performance cible. Par exemple, si une base de données P1 avec une taille maximale de 500 Go est rétrogradée en S3, un coût de stockage supplémentaire s’applique, car S3 prend en charge une taille maximale de 500 Go et la quantité de stockage incluse est de seulement 250 Go. Par conséquent, la quantité d’espace de stockage supplémentaire est de 500 Go - 250 Go = 250 Go. Pour la tarification du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse. 
* Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version différente, il est nécessaire de mettre d’abord à niveau la base de données secondaire.
* Lors de la mise à niveau descendante d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données primaires associées vers le niveau de performance inférieur souhaité avant de procéder à la mise à niveau descendante de la base de données secondaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version inférieure, il est nécessaire de mettre d’abord à niveau la base de données primaire.
* Les offres de service de restauration sont différentes selon les niveaux de service. Si vous retournez au niveau de service **De base**, la rétention des fichiers de sauvegarde sera de courte durée (consultez la section relative aux [sauvegardes Azure SQL Database](sql-database-automated-backups.md)).
* Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Base de données unique : limitations de P11 et P15 lorsque la taille maximale est supérieure à 1 To

Une taille maximale supérieure à 1 To pour les bases de données P11 et P15 est prise en charge dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, Centre de la France, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Centre de la Corée, Centre-Nord des États-Unis, Europe du Nord, Centre-Sud des États-Unis, Sud-Est asiatique, Sud du Royaume-Uni, Ouest du Royaume-Uni, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Les considérations et limitations suivantes s’appliquent aux bases de données P11 et P15 avec une taille maximale supérieure à 1 To :

- Si vous choisissez une taille maximale supérieure à 1 To lors de la création d’une base de données (avec une valeur de 4 To ou de 4096 Go), la commande de création échoue avec une erreur si la base de données est configurée dans une région non prise en charge.
- Pour les bases de données P11/P15 existantes situées dans l’une des régions prises en charge, vous pouvez augmenter la taille maximale du stockage au-delà de 1 To par incréments de 256 Go jusqu’à 4 To. Pour voir si une plus grande taille est prise en charge dans votre région, utilisez la fonction [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) ou inspectez la taille de la base de données dans le portail Azure. La mise à niveau d’une base de données P11 ou P15 existante ne peut être effectuée que par le biais d’une connexion du principal au niveau du serveur ou par les membres du rôle de base de données dbmanager. 
- En cas d’exécution d’une mise à niveau dans une région prise en charge, la configuration est mise à jour immédiatement. La base de données reste en ligne pendant le processus de mise à niveau. Toutefois, vous ne pourrez pas utiliser la totalité du stockage au-delà de 1 To tant que les fichiers de base de données réels n’ont pas été mis à niveau vers la nouvelle taille maximale. La durée requise dépend de la taille de la base de données mise à niveau. 
- Lors de la création ou de la mise à jour d’une base de données P11 ou P15, vous n’avez le choix qu’entre les tailles maximales de 1 To à 4 To par incréments de 256 Go. Lorsque vous créez une base de données P11/P15, l’option de stockage par défaut de 1 To est présélectionnée. Pour les bases de données situées dans l’une des régions prises en charge, vous pouvez augmenter le stockage maximal d’une base de données unique (nouvelle ou existante) jusqu’à un maximum de 4 To. Pour toutes les autres régions, la taille maximale ne peut pas être supérieure à 1 To. Le prix ne change pas lorsque vous sélectionnez l’option de 4 To de stockage inclus.
- Si la taille maximale d’une base de données est réglée sur une valeur supérieure à 1 To, alors elle ne peut pas être modifiée sur 1 To même si le stockage réel utilisé est inférieur à 1 To. Par conséquent, vous ne pouvez pas rétrograder un compte P11 ou P15 avec une taille maximale supérieure à 1 To vers un P11 ou P15 1 To, ou à un niveau de performance inférieur, comme P1-P6). Cette restriction s’applique également aux scénarios de restauration et de copie, notamment la limite de restauration dans le temps, la restauration géographique, la rétention de sauvegarde à long terme et la copie de bases de données. Une fois qu’une base de données est configurée avec une taille maximale supérieure à 1 To, toutes les opérations de restauration de cette base de données doivent s’effectuer vers une base de données P11/P15 avec une taille maximale supérieure à 1 To.
- Pour les scénarios de géoréplication active :
   - Configuration d’une relation de géoréplication : si la base de données primaire est de niveau P11 ou P15, la ou les bases de données secondaires doivent également être de niveau P11 ou P15. Les niveaux de performance inférieurs sont rejetés pour les bases de données secondaires, car ils ne sont pas en mesure de prendre en charge plus de 1 To.
   - Mise à niveau de la base de données primaire dans une relation de géoréplication : le fait de modifier la taille maximale de la base de données primaire pour plus de 1 To déclenche la même modification sur la base de données secondaire. Les deux mises à niveau doivent aboutir pour que la modification sur la base de données principale prenne effet. Des limitations de région pour les options de plus de 1 To s’appliquent. Si la base de données secondaire se situe dans une région qui ne prend pas en charge plus de 1 To, la base de données primaire n’est pas mise à niveau.
- L’utilisation du service Import/Export pour le chargement des bases de données P11/P15 avec plus de 1 To n’est pas prise en charge. Utilisez SqlPackage.exe pour [importer](sql-database-import.md) et [exporter](sql-database-export.md) les données.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d’informations sur les DTU et eDTU, consultez [DTU et eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Pour plus d’informations sur les limites de taille de tempdb, consultez https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
