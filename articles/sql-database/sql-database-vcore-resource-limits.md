---
title: Limites de ressources vCore d’Azure SQL Database | Microsoft Docs
description: Cette page décrit certaines limites courantes de ressources vCore d’Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/15/2018
ms.author: carlrab
ms.openlocfilehash: d225af55a705d56a94bb0e8dcfcc938b64f3633a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210798"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Limites du modèle d’achat vCore d’Azure SQL Database (préversion)

> [!IMPORTANT]
> Pour les limites du modèle d’achat DTU, consultez [Limites de ressources basées sur des unités DTU](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Base de données unique : tailles de stockage et niveaux de performance

Pour les bases de données uniques, les tableaux suivants indiquent les ressources disponibles pour une base de données unique à chaque niveau de performance et de service. Vous pouvez définir le niveau de service, le niveau de performance et la quantité de stockage pour une base de données unique à l’aide du [portail Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), de [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), de [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [d’Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) ou de [l’API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Niveau de service Usage général

#### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4
|Niveau de performance|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1536|3 072|4096|4096|
|Taille maximale du journal|307|307|461|922|1229|1229|
|Taille de TempDB (DB)|32|64|128|256|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|7000|7000|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|000
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

#### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5
|Niveau de performance|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|48|80|
|Mémoire (Go)|11|22|44|88|132|176|264|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1536|3 072|4096|4096|4096|4096|
|Taille maximale du journal|307|307|461|614|1229|1229|1229|1229|
|Taille de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|6000|7000|7000|7000|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|2 400|3200|4 800|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

### <a name="business-critical-service-tier"></a>Niveau de service critique pour l’entreprise

#### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4
|Niveau de performance|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1|2|4|8|20|36|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|1 024|1 024|
|Taille maximale du journal|307|307|307|307|307|307|
|Taille de TempDB (DB)|32|64|128|256|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|80000|120 000|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|3|3|3|3|3|3|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

#### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5
|Niveau de performance|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|48|80|
|Mémoire (Go)|11|22|44|88|132|176|264|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1 571|3 142|6 284|15 768|25 252|37 936|68 104|131,64|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|2 048|4096|4096|4096|
|Taille maximale du journal|307|307|307|307|614|1229|1229|1229|
|Taille de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|60000|80000|120 000|200000
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|2 400|3200|4 800|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

## <a name="single-database-change-storage-size"></a>Base de données unique : modifier la taille de stockage

- Le stockage peut être approvisionné jusqu’à la limite de taille maximale par incréments de 1 Go. Le stockage de données minimum configurable est de 5 Go 
- Vous pouvez configurer du stockage pour une base de données unique en augmentant ou en diminuant sa taille maximale à l’aide du [portail Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), de [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az_sql_db_update) ou de [l’API REST](/rest/api/sql/databases/update).
- SQL Database alloue automatiquement 30 % de stockage supplémentaire pour les fichiers journaux et 32 Go par vCore pour TempDB, dans une limite de 384 Go. TempDB se trouve sur un SSD attaché pour tous les niveaux de service.
- Le prix du stockage pour une base de données unique est égal à la somme des volumes de stockage des données et de stockage des journaux multipliée par le prix unitaire du stockage pour le niveau de service. Le coût de TempDB est inclus dans le prix de vCore. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Base de données unique : modifier les vCore

Après la sélection initiale du nombre de vCore, vous pouvez mettre à l’échelle une base de données unique de façon dynamique en fonction de l’expérience réelle à l’aide du [portail Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), de [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az_sql_db_update) ou de [l’API REST](/rest/api/sql/databases/update). 

La modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Le délai de basculement peut varier, mais il est généralement de moins de 4 secondes, et ne dépassera pas les 30 secondes dans 99 % des cas. Ce délai peut se révéler supérieur, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées. 

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service usage général ne demande pas plus de six heures. Le changement des niveaux de performance d’une base de données de la même taille dans le niveau de service critique pour l’entreprise doit s’effectuer en moins de trois heures.

> [!TIP]
> Pour surveiller les opérations en cours, consultez la [gestion des opérations à l’aide de l’API REST SQL](/rest/api/sql/Operations/List), la [gestion des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), la [surveillance des opérations à l’aide T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), ainsi que ces deux commandes PowerShell : [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) et [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Si vous effectuez la mise à niveau vers un niveau de service ou de performance supérieur, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande (maxsize).
* Pour pouvoir passer à une version antérieure, l’espace utilisé par la base de données doit être inférieur à la taille et au niveau de performance autorisés par le niveau de service voulu. 
* Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version différente, il est nécessaire de mettre d’abord à niveau la base de données secondaire.
* Lors de la mise à niveau descendante d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données primaires associées vers le niveau de performance inférieur souhaité avant de procéder à la mise à niveau descendante de la base de données secondaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version inférieure, il est nécessaire de mettre d’abord à niveau la base de données primaire.
* Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool élastique : tailles de stockage et niveaux de performance

Pour les pools élastiques SQL Database, les tableaux suivants indiquent les ressources disponibles à chaque niveau de performance et de service. Vous pouvez définir le niveau de service, le niveau de performance et la quantité de stockage à l’aide du [portail Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), de [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [d’Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) ou de [l’API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Les limites de ressources des bases de données individuelles dans les pools élastiques sont généralement identiques à celles des bases de données uniques situées hors des pools qui ont le même niveau de performance. Par exemple, le nombre maximal de workers simultanés dans une base de données GP_Gen4_1 est de 200. Par conséquent, le nombre maximal de workers simultanés pour une base de données dans un pool GP_Gen4_1 est aussi de 200. Notez que le nombre total de workers simultanés dans le pool GP_Gen4_1 est de 210.

### <a name="general-purpose-service-tier"></a>Niveau de service Usage général

#### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4
|Niveau de performance|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Taille maximale des données (Go)|512|756|1536|2 048|3584|4096|
|Taille maximale du journal|154|227|461|614|1075|1229|
|Taille de TempDB (DB)|32|64|128|256|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|7000|7000|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|3360|5040|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Densité de pool maximale|100|200|500|500|500|500|
|Valeurs min/max de pool élastique|0 ; 0,25 ; 0,5 ; 1|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

#### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5
|Niveau de performance|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|48|80|
|Mémoire (Go)|11|22|44|88|132|176|264|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Taille maximale des données (Go)|512|756|1536|2 048|3 072|4096|4096|4096|
|Taille maximale du journal|154|227|461|614|922|1229|1229|1229|
|Taille de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|6000|7000|7000|7000|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|2520|3360|5040|8400
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Densité de pool maximale|100|200|500|500|500|500|500|500|
|Valeurs min/max de pool élastique|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|Nombre de réplicas|1|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

### <a name="business-critical-service-tier"></a>Niveau de service critique pour l’entreprise

#### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4
|Niveau de performance|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1|2|4|8|20|36|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|1 024|1 024|
|Taille maximale du journal|307|307|307|307|307|307|
|Taille de TempDB (DB)|32|64|128|256|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|80000|120 000|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|3360|5040|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Densité de pool maximale|N/A|50|100|100|100|100|
|Valeurs min/max de pool élastique|N/A|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

#### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5
|Niveau de performance|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|48|80|
|Mémoire (Go)|11|22|44|88|132|176|264|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1 571|3 142|6 284|15 768|25 252|37 936|68 104|131,64|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|2 048|4096|4096|4096|
|Taille maximale du journal|307|307|307|307|614|1229|1229|1229|
|Taille de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|60000|80000|120 000|200000
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|2520|3360|5040|8400|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Densité de pool maximale|N/A|50|100|100|100|100|100|100|
|Valeurs min/max de pool élastique|N/A|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

Si tous les vCore d’un pool élastique sont occupés, chaque base de données du pool reçoit une quantité égale de ressources de calcul pour traiter les requêtes. Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. Le partage équitable des ressources du pool élastique s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de vCore par base de données est défini sur une valeur différente de zéro.

### <a name="database-properties-for-pooled-databases"></a>Propriétés de base de données pour les bases de données mises en pool

Le tableau suivant décrit les propriétés des bases de données mises en pool.

| Propriété | Description |
|:--- |:--- |
| Nombre maximal de vCore par base de données |Nombre maximal de vCore pouvant être utilisés par une des bases de données du pool en fonction du nombre de vCore utilisés par les autres bases de données du pool. Le nombre maximal de vCore par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de données. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal de vCore par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation.|
| Nombre minimal de vCore par base de données |Nombre minimal de vCore garanti pour chaque base de données du pool. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal de vCore par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est définie sur une valeur comprise entre 0 et le nombre moyen de vCore utilisés par base de données. Le produit du nombre de bases de données du pool et du nombre minimal de vCore par base de données ne peut pas dépasser le nombre de vCore par pool.|
| Espace de stockage maximal par base de données |La taille de base de données maximale définie par l’utilisateur pour une base de données dans un pool. Les bases de données regroupées se partagent l’espace de stockage du pool alloué. Par conséquent, la taille qu’une base de données peut atteindre est limitée au stockage de pool minimal restant et à la taille de base de données. La taille de base de données maximale fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool élastique : modifier la taille du stockage

- Le stockage peut être approvisionné jusqu’à la limite de taille maximale : 
 - Pour le stockage Standard, augmenter ou diminuer la taille par incréments de 10 Go
 - Pour le stockage Premium, augmenter ou diminuer la taille par incréments de 250 Go
- Vous pouvez configurer du stockage supplémentaire pour un pool élastique en augmentant ou en diminuant sa taille maximale à l’aide du [portail Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [d’Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou de [l’API REST](/rest/api/sql/elasticpools/update).
- Le prix du stockage pour un pool élastique est égal au volume de stockage multiplié par le prix unitaire du stockage pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Pool élastique : modifier les vCore

Vous pouvez augmenter ou diminuer le niveau de performance pour un pool élastique en fonction des besoins de la ressource à l’aide du [portail Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [d’Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou de [l’API REST](/rest/api/sql/elasticpools/update).

- Lors de la remise à l’échelle des vCore du pool, les connexions de base de données sont brièvement interrompues. Il s’agit du même comportement que celui qui se produit lors de la remise à l’échelle des DTU pour une base de données (qui n’est pas dans un pool). Pour plus d’informations sur la durée et l’impact des pertes de connexion pour une base de données lors des opérations de remise à l’échelle, consultez [Remise à l’échelle des DTU pour une base de données unique](#single-database-change-storage-size). 
- La durée de remise à l’échelle des vCore du pool peut dépendre de la quantité totale d’espace de stockage utilisée par toutes les bases de données dans le pool. En règle générale, la latence de remise à l’échelle moyenne est de 90 minutes ou moins pour 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données du pool est égal à 200 Go, une opération de redimensionnement du pool prend 3 heures au maximum. Dans certains cas dans le niveau Standard ou De base, la latence de remise à l’échelle peut être de moins de cinq minutes, quelle que soit la quantité d’espace utilisé.
- En général, la durée de modification du nombre minimal de vCore par base de données ou du nombre maximal de vCore par base de données prend cinq minutes au maximum.
- Lors de la réduction des vCore d’un pool, l’espace utilisé par le pool doit être inférieur à la taille maximale autorisée du niveau de service cible et des vCore maximum du pool.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Quel est le nombre maximal de serveurs et de bases de données ?

| Maximale | Valeur |
| :--- | :--- |
| Bases de données par serveur | 5 000 |
| Nombre de serveurs par région et par abonnement | 20 |
|||

> [!IMPORTANT]
> Le nombre de bases de données approchant la limite par serveur, les éléments suivants peuvent se produire :
> <br> • Augmentation de latence dans l’exécution de requêtes sur la base de données master.  Cela inclut les vues de statistiques d’utilisation des ressources telles que sys.resource_stats.
> <br> • Augmentation de latence des opérations de gestion et le rendu des points de vue de portails qui impliquent des bases de données sur le serveur.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Que se passe-t-il lorsque les limites de ressources d’une base de données ou d’un pool élastique sont atteintes ?

### <a name="compute-vcores"></a>Calculer (vCore)

Lorsque l’utilisation du calcul de la base de données (exprimée en utilisation de vCore) est élevée, la latence de la requête augmente et peut même causer une expiration du délai d’attente. Dans ces conditions, les requêtes peuvent être mises en file d’attente par le service et reçoivent des ressources pour leur exécution lorsque des ressources se libèrent.
En cas d’utilisation élevée des calculs, voici certaines des options d’atténuation à votre disposition :

- Augmenter le niveau de performance de la base de données ou d’un pool élastique pour fournir plus de vCore à la base de données. Consultez [Base de données unique : modifier les vCore](#single-database-change-vcores) et [Pool élastique : modifier les vCore](#elastic-pool-change-vcores).
- Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Stockage

Lorsque l’espace de base de données utilisé atteint la limite de taille maximale, les insertions et mises à jour de base de données qui augmentent la taille des données échouent et les clients reçoivent un [message d’erreur](sql-database-develop-error-messages.md). Les opérations de type SELECT et DELETE sur la base de données continuent à aboutir.

En cas d’utilisation élevée de l’espace, voici certaines des options d’atténuation à votre disposition :

- Augmenter la taille maximale de la base de données ou du pool élastique ou modifier le niveau de performance pour augmenter l’espace de stockage maximum. Voir [Limites de ressources vCore d’Azure SQL Database](sql-database-vcore-resource-limits.md).
- Si la base de données est dans un pool élastique, vous pouvez également déplacer la base de données en dehors du pool afin que son espace de stockage ne soit pas partagé avec d’autres bases de données.

### <a name="sessions-and-workers-requests"></a>Sessions et workers (requêtes) 

Le nombre maximal de sessions et de workers est déterminé par les niveaux de performance et de service. Lorsque les limites de sessions ou de workers sont atteintes, les nouvelles requêtes sont refusées et les clients reçoivent un message d’erreur. Si le nombre de connexions disponibles peut être contrôlé par l’application, le nombre de workers simultanés est souvent plus difficile à estimer et à contrôler. Cela est particulièrement vrai pendant les pics de charge lorsque les limites de ressources de base de données sont atteintes et que les workers s’accumulent en raison de requêtes plus longues à exécuter. 

En cas d’utilisation élevée de workers ou de sessions, voici certaines des options d’atténuation à votre disposition :
- Augmenter le niveau de performance ou le niveau de service du pool élastique ou de la base de données. Consultez [Base de données unique : modifier la taille de stockage](#single-database-change-storage-size), [Base de données unique : modifier les vCore](#single-database-change-vcores), [Pool élastique : modifier la taille du stockage](#elastic-pool-change-storage-size) et [Pool élastique : modifier les vCore](#elastic-pool-change-vcores).
- Optimiser les requêtes afin de réduire l’utilisation des ressources de chaque requête si la cause de l’utilisation du travail accrue est un problème de contention des ressources de calcul. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Étapes suivantes

- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
