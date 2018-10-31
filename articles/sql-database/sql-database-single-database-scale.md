---
title: Mise à l'échelle de ressources de singletons Azure SQL Database unique | Microsoft Docs
description: Cet article décrit comment faire évoluer les ressources de calcul et de stockage disponibles pour un singleton dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 258f8fbe8d99923240db8d6d10c4cf812c939510
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466884"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Mise à l'échelle de ressources de singletons Azure SQL Database unique

Cet article décrit comment faire évoluer les ressources de calcul et de stockage disponibles pour un singleton dans Azure SQL Database.

## <a name="vcore-based-purchasing-model-change-storage-size"></a>Modèle d’achat vCore : modifier la taille de stockage

- Le stockage peut être approvisionné jusqu’à la limite de taille maximale par incréments de 1 Go. Le stockage de données minimum configurable est de 5 Go.
- Vous pouvez configurer du stockage pour une base de données unique en augmentant ou en diminuant sa taille maximale à l’aide du [portail Azure](https://portal.azure.com), de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az-sql-db-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database alloue automatiquement 30 % de stockage supplémentaire pour les fichiers journaux et 32 Go par vCore pour TempDB, dans une limite de 384 Go. TempDB se trouve sur un SSD attaché pour tous les niveaux de service.
- Le prix du stockage pour une base de données unique est égal à la somme des volumes de stockage des données et de stockage des journaux multipliée par le prix unitaire du stockage pour le niveau de service. Le coût de TempDB est inclus dans le prix de vCore. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>Modèle d’achat vCore : modifier les ressources de calcul

Après la sélection initiale du nombre de vCore, vous pouvez mettre à l’échelle une base de données unique de façon dynamique en fonction de l’expérience réelle à l’aide du [portail Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-server), de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az-sql-db-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

La modification du niveau de service et/ou de la taille de calcul d’une base de données crée un réplica de la base de données d’origine à la nouvelle taille de calcul, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Le délai de basculement peut varier, mais il est généralement de moins de 4 secondes, et ne dépassera pas les 30 secondes dans 99 % des cas. Ce délai peut se révéler supérieur, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service usage général ne demande pas plus de six heures. Le changement de la taille de calcul d’une base de données de la même taille dans le niveau de service critique pour l’entreprise doit s’effectuer en moins de trois heures.

> [!TIP]
> Pour surveiller les opérations en cours, consultez la [gestion des opérations à l’aide de l’API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), la [gestion des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), la [surveillance des opérations à l’aide de T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), ainsi que ces deux commandes PowerShell : [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) et [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Si vous effectuez la mise à niveau vers un niveau de service ou une taille de calcul supérieurs, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande (maxsize).
- Pour pouvoir passer à une version antérieure, l’espace utilisé par la base de données doit être inférieur à la taille maximale autorisée pour le service cible et la taille de calcul.
- Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de service et la taille de calcul souhaités avant de procéder à la mise à niveau de la base de données primaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version différente, il est nécessaire de mettre d’abord à niveau la base de données secondaire.
- Lors de la mise à niveau descendante d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données primaires associées vers le niveau de service et la taille de calcul inférieurs souhaités avant de procéder à la mise à niveau descendante de la base de données secondaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version inférieure, il est nécessaire de mettre d’abord à niveau la base de données primaire.
- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Modèle d’achat DTU : modifier la taille de stockage

- Le prix des DTU pour une base de données unique inclut une certaine quantité de stockage sans coût supplémentaire. Un espace de stockage en plus du volume inclus peut être approvisionné pour un coût supplémentaire jusqu’à la limite de taille par incréments de 250 Go jusqu’à 1 To, puis par incréments de 256 Go au-delà de 1 To. Pour les quantités de stockage et limites de taille maximale incluses, consultez l’article [Base de données unique : tailles de stockage et tailles de calcul](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Vous pouvez approvisionner un espace de stockage supplémentaire pour une base de données unique en augmentant la taille maximale à l’aide du Portail Azure, de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az-sql-db-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Le prix de l’espace de stockage supplémentaire pour une base de données unique est égal au volume de stockage supplémentaire multiplié par le prix unitaire du stockage supplémentaire pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Modèle d’achat DTU : modifier les ressources de calcul (DTU)

Après la sélection initiale d’un niveau de service, d’une taille de calcul et d’une quantité de stockage, vous pouvez procéder à la montée ou descente en puissance d’une base de données de manière dynamique en fonction de l’expérience réelle à l’aide du Portail Azure, de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az-sql-db-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

La vidéo suivante montre la modification dynamique du niveau de service et de la taille de calcul pour augmenter les DTU disponibles pour une base de données unique.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modification du niveau de service et/ou de la taille de calcul d’une base de données crée un réplica de la base de données d’origine à la nouvelle taille de calcul, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Le délai de basculement peut varier, mais il est inférieur à 30 secondes dans 99 % des cas. Ce délai peut se révéler supérieur, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de six heures. Le changement des tailles de calcul d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de trois heures.

> [!TIP]
> Pour surveiller les opérations en cours, consultez la [gestion des opérations à l’aide de l’API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), la [gestion des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), la [surveillance des opérations à l’aide de T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), ainsi que ces deux commandes PowerShell : [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) et [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Si vous effectuez la mise à niveau vers un niveau de service ou une taille de calcul supérieurs, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande (maxsize).
- Pour pouvoir passer à une version antérieure, l’espace utilisé par la base de données doit être inférieur à la taille maximale autorisée pour le service cible et la taille de calcul.
- Lorsque vous rétrogradez du niveau **Premium** vers le niveau **Standard**, un coût de stockage supplémentaire s’applique si (1) la taille maximale de la base de données est prise en charge dans la taille de calcul cible et (2) la taille maximale dépasse la quantité de stockage incluse dans la taille de calcul cible. Par exemple, si une base de données P1 avec une taille maximale de 500 Go est rétrogradée en S3, un coût de stockage supplémentaire s’applique, car S3 prend en charge une taille maximale de 500 Go et la quantité de stockage incluse est de seulement 250 Go. Par conséquent, la quantité d’espace de stockage supplémentaire est de 500 Go - 250 Go = 250 Go. Pour la tarification du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse.
- Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de service et la taille de calcul souhaités avant de procéder à la mise à niveau de la base de données primaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version différente, il est nécessaire de mettre d’abord à niveau la base de données secondaire.
- Lors de la mise à niveau descendante d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données primaires associées vers le niveau de service et la taille de calcul inférieurs souhaités avant de procéder à la mise à niveau descendante de la base de données secondaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version inférieure, il est nécessaire de mettre d’abord à niveau la base de données primaire.
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous retournez au niveau de service **De base**, la rétention des fichiers de sauvegarde sera de plus courte durée. Consultez la section relative aux [sauvegardes Azure SQL Database](sql-database-automated-backups.md).
- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Modèle d’achat DTU : limitations de P11 et P15 lorsque la taille maximale est supérieure à 1 To

Une taille maximale supérieure à 1 To pour les bases de données P11 et P15 est prise en charge dans les régions suivantes : Australie Est, Australie Sud-Est, Brésil Sud, Canada Centre, Canada Est, USA Centre, France Centre, Allemagne Centre, Japon Est, Japon Ouest, Corée Centre, USA Centre Nord, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Royaume-Uni Ouest, USA Est 2, USA Ouest, US Gov Virginie et Europe Ouest. Les considérations et limitations suivantes s’appliquent aux bases de données P11 et P15 avec une taille maximale supérieure à 1 To :

- Si vous choisissez une taille maximale supérieure à 1 To lors de la création d’une base de données (avec une valeur de 4 To ou de 4096 Go), la commande de création échoue avec une erreur si la base de données est configurée dans une région non prise en charge.
- Pour les bases de données P11/P15 existantes situées dans l’une des régions prises en charge, vous pouvez augmenter la taille maximale du stockage au-delà de 1 To par incréments de 256 Go jusqu’à 4 To. Pour voir si une plus grande taille est prise en charge dans votre région, utilisez la fonction [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) ou inspectez la taille de la base de données dans le portail Azure. La mise à niveau d’une base de données P11 ou P15 existante ne peut être effectuée que par le biais d’une connexion du principal au niveau du serveur ou par les membres du rôle de base de données dbmanager.
- En cas d’exécution d’une mise à niveau dans une région prise en charge, la configuration est mise à jour immédiatement. La base de données reste en ligne pendant le processus de mise à niveau. Toutefois, vous ne pourrez pas utiliser la totalité du stockage au-delà de 1 To tant que les fichiers de base de données réels n’ont pas été mis à niveau vers la nouvelle taille maximale. La durée requise dépend de la taille de la base de données mise à niveau.
- Lors de la création ou de la mise à jour d’une base de données P11 ou P15, vous n’avez le choix qu’entre les tailles maximales de 1 To à 4 To par incréments de 256 Go. Lorsque vous créez une base de données P11/P15, l’option de stockage par défaut de 1 To est présélectionnée. Pour les bases de données situées dans l’une des régions prises en charge, vous pouvez augmenter le stockage maximal d’une base de données unique (nouvelle ou existante) jusqu’à un maximum de 4 To. Pour toutes les autres régions, la taille maximale ne peut pas être supérieure à 1 To. Le prix ne change pas lorsque vous sélectionnez l’option de 4 To de stockage inclus.
- Si la taille maximale d’une base de données est réglée sur une valeur supérieure à 1 To, alors elle ne peut pas être modifiée sur 1 To même si le stockage réel utilisé est inférieur à 1 To. Par conséquent, vous ne pouvez pas rétrograder un compte P11 ou P15 avec une taille maximale supérieure à 1 To vers un P11 ou P15 1 To, ou à une taille de calcul inférieure, comme P1-P6). Cette restriction s’applique également aux scénarios de restauration et de copie, notamment la limite de restauration dans le temps, la restauration géographique, la rétention de sauvegarde à long terme et la copie de bases de données. Une fois qu’une base de données est configurée avec une taille maximale supérieure à 1 To, toutes les opérations de restauration de cette base de données doivent s’effectuer vers une base de données P11/P15 avec une taille maximale supérieure à 1 To.
- Pour les scénarios de géoréplication active :
  - Configuration d’une relation de géoréplication : si la base de données primaire est de niveau P11 ou P15, la ou les bases de données secondaires doivent également être de niveau P11 ou P15. Les tailles de calcul inférieures sont rejetées pour les bases de données secondaires, car elles ne sont pas en mesure de prendre en charge plus de 1 To.
  - Mise à niveau de la base de données primaire dans une relation de géoréplication : le fait de modifier la taille maximale de la base de données primaire pour plus de 1 To déclenche la même modification sur la base de données secondaire. Les deux mises à niveau doivent aboutir pour que la modification sur la base de données principale prenne effet. Des limitations de région pour les options de plus de 1 To s’appliquent. Si la base de données secondaire se situe dans une région qui ne prend pas en charge plus de 1 To, la base de données primaire n’est pas mise à niveau.
- L’utilisation du service Import/Export pour le chargement des bases de données P11/P15 avec plus de 1 To n’est pas prise en charge. Utilisez SqlPackage.exe pour [importer](sql-database-import.md) et [exporter](sql-database-export.md) les données.

## <a name="next-steps"></a>Étapes suivantes

Pour les limites de ressources globales, consultez [SQL Database vCore-based resource limits - elastic pools](sql-database-vcore-resource-limits-single-databases.md) (limites de ressource vCore SQL Database : singleton) et [SQL Database DTU-based resource limits - elastic pools](sql-database-dtu-resource-limits-single-databases.md) (limites de ressource DTU SQL Database : pools élastiques).
