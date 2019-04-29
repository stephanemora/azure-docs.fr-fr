---
title: Mise à l'échelle de ressources de singletons Azure SQL Database unique | Microsoft Docs
description: Cet article décrit comment faire évoluer les ressources de calcul et de stockage disponibles pour un singleton dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 471ded9cd94623929630155f1a3c613bf00576a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331818"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Mise à l'échelle de ressources de singletons Azure SQL Database unique

Cet article décrit comment faire évoluer les ressources de calcul et de stockage disponibles pour un singleton dans Azure SQL Database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

## <a name="change-compute-size-vcores-or-dtus"></a>Modifier la taille de calcul (vCores ou dtu)

Après la sélection initiale du nombre de dtu ou de vCores, vous pouvez faire évoluer une base de données unique ou descendre en puissance dynamiquement en fonction de l’expérience réelle à l’aide de la [Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), le [Azure CLI](/cli/azure/sql/db#az-sql-db-update), ou le [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

La vidéo suivante montre la modification dynamique du niveau de service et de la taille de calcul pour augmenter les DTU disponibles pour une base de données unique.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impact du changement de taille de service de niveau ou de remise à l’échelle de calcul

Modification du service de niveau ou de calculer la taille d’une base de données implique principalement le service, procédez comme suit :

1. Créer la nouvelle instance de calcul pour la base de données  

    Une nouvelle instance de calcul pour la base de données est créée avec la taille de calcul et le niveau de service demandé. Pour certaines combinaisons de niveau de service et les modifications de taille de calcul, un réplica de la base de données doit être créé dans la nouvelle instance de calcul qui implique la copie des données et peut influencer fortement la latence globale. Tous les cas, la base de données reste en ligne pendant cette étape et connexions continuent à être dirigé vers la base de données dans l’instance de calcul d’origine.

2. Basculer le routage des connexions à la nouvelle instance de calcul

    Les connexions existantes à la base de données dans l’instance de calcul d’origine sont supprimées. De nouvelles connexions sont établies à la base de données dans la nouvelle instance de calcul. Pour certaines combinaisons de niveau de service et les modifications de taille de calcul, fichiers de base de données détachez et rattachez pendant ce basculement.  Malgré tout, le commutateur peut entraîner une brève interruption de service lors de la base de données n’est pas disponible généralement moins de 30 secondes et souvent que quelques secondes. S’il existe de longues transactions en cours d’exécution lorsque les connexions sont supprimées, la durée de cette étape peut prendre plus de temps pour permettre la récupération des transactions abandonnées. [Accélération de récupération de base de données](sql-database-accelerated-database-recovery.md) peuvent réduire l’impact de l’abandon de transactions de longue.

> [!IMPORTANT]
> Aucune donnée n’est perdue au cours de des étapes dans le flux de travail.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence de modification de la taille de service de niveau ou de remise à l’échelle de calcul

La latence pour modifier le niveau de service ou de mettre à l’échelle la taille de calcul d’une base de données unique ou d’un pool élastique est paramétrée comme suit :

|Niveau de service|Base de données unique simple,</br>Standard (S0-S1)|Pool élastique de base,</br>Standard (S2-S12), </br>Très grande échelle, </br>Général objectif seule base de données ou pool élastique|Pool élastique ou base de données unique Premium ou critique pour l’entreprise|
|:---|:---|:---|:---|
|**Base de données unique base,</br> Standard (S0-S1)**|&bull; &nbsp;Latence de temps constant indépendante de l’espace utilisé</br>&bull; &nbsp;En règle générale, moins de 5 minutes|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|
|**Pool élastique de base, </br>Standard (S2-S12), </br>Hyperscale, </br>pool élastique ou base de données unique à usage général**|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence de temps constant indépendante de l’espace utilisé</br>&bull; &nbsp;En règle générale, moins de 5 minutes|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|
|**Pool élastique ou base de données unique Premium ou critique pour l’entreprise**|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|

> [!TIP]
> Pour surveiller des opérations en cours, voir : [Gérer des opérations à l’aide de l’API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gérer des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), [Surveiller des opérations à l’aide de T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) et les deux commandes PowerShell suivantes : [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) et [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>L’annulation des modifications de niveau de service ou des opérations de remise à l’échelle de calcul

Un niveau de service, modifiez ou calculer la remise à l’échelle d’opération peut être annulée.

#### <a name="azure-portal"></a>Portail Azure

Dans le panneau Vue d’ensemble de la base de données, accédez à **Notifications** , puis cliquez sur la vignette indiquant une opération en cours est :

![Opération en cours](media/sql-database-single-database-scale/ongoing-operations.png)

Ensuite, cliquez sur le bouton intitulé **annuler cette opération**.

![Annuler l’opération en cours](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

À partir d’une invite de commandes PowerShell, définissez le `$ResourceGroupName`, `$ServerName`, et `$DatabaseName`, puis exécutez la commande suivante :

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considérations supplémentaires lors de la modification de taille de calcul de niveau ou la remise à l’échelle du service

- Si vous effectuez la mise à niveau vers un niveau de service ou une taille de calcul supérieurs, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande (maxsize).
- Pour pouvoir passer à une version antérieure, l’espace utilisé par la base de données doit être inférieur à la taille maximale autorisée pour le service cible et la taille de calcul.
- Lorsque vous rétrogradez du niveau **Premium** vers le niveau **Standard**, un coût de stockage supplémentaire s’applique si (1) la taille maximale de la base de données est prise en charge dans la taille de calcul cible et (2) la taille maximale dépasse la quantité de stockage incluse dans la taille de calcul cible. Par exemple, si une base de données P1 avec une taille maximale de 500 Go est rétrogradée en S3, un coût de stockage supplémentaire s’applique, car S3 prend en charge une taille maximale de 500 Go et la quantité de stockage incluse est de seulement 250 Go. Par conséquent, la quantité de stockage supplémentaire est de 500 Go - 250 Go = 250 Go. Pour la tarification du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse.
- Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de service et la taille de calcul souhaités avant de procéder à la mise à niveau de la base de données primaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version différente, il est nécessaire de mettre d’abord à niveau la base de données secondaire.
- Lors de la mise à niveau descendante d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données primaires associées vers le niveau de service et la taille de calcul inférieurs souhaités avant de procéder à la mise à niveau descendante de la base de données secondaire (conseil général pour de meilleures performances). Lors de la mise à niveau vers une version inférieure, il est nécessaire de mettre d’abord à niveau la base de données primaire.
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous repassez au niveau de service **De base**, la rétention des fichiers de sauvegarde sera de plus courte durée. Consultez l’article relatif aux [sauvegardes Azure SQL Database](sql-database-automated-backups.md).
- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

### <a name="billing-during-compute-rescaling"></a>Facturation lors de la remise à l’échelle de calcul

Vous êtes facturé pour chaque heure d’existence de la base de données avec le niveau de service le plus élevé, la taille de calcul appliquée pendant cette heure quel que soit l’usage, ou si la base de données a été active pendant moins d’une heure. Par exemple, si vous avez créé une base de données unique et que vous l’avez supprimée cinq minutes après, votre facture mentionne le coût d’une heure de base de données.

## <a name="change-storage-size"></a>Modifier la taille de stockage

### <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

- Le stockage peut être approvisionné jusqu’à la limite de taille maximale par incréments de 1 Go. Le stockage de données minimum configurable est de 5 Go.
- Vous pouvez configurer du stockage pour une base de données unique en augmentant ou en diminuant sa taille maximale à l’aide du [portail Azure](https://portal.azure.com), de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), de [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az-sql-db-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database alloue automatiquement 30 % de stockage supplémentaire pour les fichiers journaux et 32 Go par vCore pour TempDB, dans une limite de 384 Go. TempDB se trouve sur un SSD attaché pour tous les niveaux de service.
- Le prix du stockage pour une base de données unique est égal à la somme des quantités de stockage des données et de stockage des journaux multipliée par le prix unitaire du stockage pour le niveau de service. Le coût de TempDB est inclus dans le prix de vCore. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

- Le prix des DTU pour une base de données unique inclut une certaine quantité de stockage sans coût supplémentaire. Un espace de stockage en plus du volume inclus peut être approvisionné pour un coût supplémentaire jusqu’à la limite de taille par incréments de 250 Go jusqu’à 1 To, puis par incréments de 256 Go au-delà de 1 To. Pour les quantités de stockage et limites de taille maximale incluses, voir l’article [Base de données unique : tailles de stockage et tailles de calcul](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Vous pouvez approvisionner un espace de stockage supplémentaire pour une base de données unique en augmentant la taille maximale à l’aide du Portail Azure, de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), de [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az-sql-db-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Le prix de l’espace de stockage supplémentaire pour une base de données unique est égal à la quantité de stockage supplémentaire multiplié par le prix unitaire du stockage supplémentaire pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Contraintes de P11 et P15 lorsque max taille est supérieure à 1 to

Un espace de stockage supérieur à 1 To au niveau Premium est actuellement disponible dans les toutes régions sauf les suivantes : Chine Est, Chine Nord, Allemagne Centre, Allemagne Nord-Est, USA Centre-Ouest, US DoD et Gouvernement US Centre. Dans ces régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Les considérations et limitations suivantes s’appliquent aux bases de données P11 et P15 avec une taille maximale supérieure à 1 To :

- Si la taille maximale pour une base de données P11 ou P15 était jamais une valeur supérieure à 1 To, puis pouvez il uniquement être restauré ou copié vers une base de données P11 ou P15.  Par la suite, la base de données permettre être remises à l’échelle à une taille de calcul différente fournie par la quantité d’espace allouée au moment de l’opération de remise à l’échelle ne dépasse pas les limites de taille maximale de la nouvelle taille de calcul.
- Pour les scénarios de géoréplication active :
  - Configuration d’une relation de géoréplication : Si la base de données primaire est de niveau P11 ou P15, la ou les bases de données secondaires doivent également être de niveau P11 ou P15. Les tailles de calcul inférieures sont rejetées pour les bases de données secondaires, car elles ne sont pas en mesure de prendre en charge plus de 1 To.
  - Mise à niveau de la base de données primaire dans une relation de géoréplication : Le fait de modifier la taille maximale de la base de données primaire pour plus de 1 To déclenche la même modification sur la base de données secondaire. Les deux mises à niveau doivent aboutir pour que la modification sur la base de données principale prenne effet. Des limitations de région pour les options de plus de 1 To s’appliquent. Si la base de données secondaire se situe dans une région qui ne prend pas en charge plus de 1 To, la base de données primaire n’est pas mise à niveau.
- L’utilisation du service Import/Export pour le chargement des bases de données P11/P15 avec plus de 1 To n’est pas prise en charge. Utilisez SqlPackage.exe pour [importer](sql-database-import.md) et [exporter](sql-database-export.md) les données.

## <a name="next-steps"></a>Étapes suivantes

Pour les limites de ressources globales, consultez [SQL Database vCore-based resource limits - elastic pools](sql-database-vcore-resource-limits-single-databases.md) (limites de ressource vCore SQL Database : singleton) et [SQL Database DTU-based resource limits - elastic pools](sql-database-dtu-resource-limits-single-databases.md) (limites de ressource DTU SQL Database : pools élastiques).
