---
title: Mettre à l’échelle des ressources de pool élastique - Azure SQL Database | Microsoft Docs
description: Cette page décrit les ressources de mise à l’échelle pour les pools élastiques dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848079"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Mettre à l’échelle un pool élastique dans Azure SQL Database

Cet article décrit comment faire évoluer les ressources de calcul et de stockage disponibles pour les pools élastiques et les bases de données mises en pool dans Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Modification des ressources de calcul (vCores ou dtu)

Après la sélection initiale le nombre de vCores ou Edtu, vous pouvez faire évoluer un pool élastique ou descendre en puissance dynamiquement en fonction de l’expérience réelle à l’aide de la [Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), le [Azure CLI ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou le [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impact du changement de taille de service de niveau ou de remise à l’échelle de calcul

Modification du service de niveau ou de calcul de taille d’un pool élastique suit un modèle similaire que pour les bases de données uniques et principalement implique que le service effectuant les étapes suivantes :

1. Créer la nouvelle instance de calcul pour le pool élastique  

    Une nouvelle instance de calcul pour le pool élastique est créée avec la taille de calcul et le niveau de service demandé. Pour certaines combinaisons de niveau de service et les modifications de taille de calcul, un réplica de chaque base de données doit être créé dans la nouvelle instance de calcul qui implique la copie des données et peut influencer fortement la latence globale. Malgré tout, les bases de données restent en ligne pendant cette étape, et connexions continuent d’être dirigés vers les bases de données dans l’instance de calcul d’origine.

2. Basculer le routage des connexions à la nouvelle instance de calcul

    Les connexions existantes aux bases de données dans l’instance de calcul d’origine sont supprimées. De nouvelles connexions sont établies pour les bases de données dans la nouvelle instance de calcul. Pour certaines combinaisons de niveau de service et les modifications de taille de calcul, fichiers de base de données détachez et rattachez pendant ce basculement.  Malgré tout, le commutateur peut entraîner une brève interruption de service lorsque les bases de données ne sont pas disponibles généralement moins de 30 secondes et souvent que quelques secondes. S’il existe de longues transactions en cours d’exécution lorsque les connexions sont supprimées, la durée de cette étape peut prendre plus de temps pour permettre la récupération des transactions abandonnées. [Accélération de récupération de base de données](sql-database-accelerated-database-recovery.md) peuvent réduire l’impact de l’abandon de transactions de longue.

> [!IMPORTANT]
> Aucune donnée n’est perdue au cours de des étapes dans le flux de travail.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence de modification de la taille de service de niveau ou de remise à l’échelle de calcul

La latence pour modifier le niveau de service ou de mettre à l’échelle la taille de calcul d’une base de données unique ou d’un pool élastique est paramétrée comme suit :

|Niveau de service|Base de données unique simple,</br>Standard (S0-S1)|Pool élastique de base,</br>Standard (S2-S12), </br>Très grande échelle, </br>Général objectif seule base de données ou pool élastique|Pool élastique ou base de données unique Premium ou critique pour l’entreprise|
|:---|:---|:---|:---|
|**Base de données unique base,</br> Standard (S0-S1)**|&bull; &nbsp;Latence de temps constant indépendante de l’espace utilisé</br>&bull; &nbsp;En règle générale, moins de 5 minutes|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|
|**Pool élastique de base, </br>Standard (S2-S12), </br>Hyperscale, </br>pool élastique ou base de données unique à usage général**|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence de temps constant indépendante de l’espace utilisé</br>&bull; &nbsp;En règle générale, moins de 5 minutes|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|
|**Pool élastique ou base de données unique Premium ou critique pour l’entreprise**|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|&bull; &nbsp;Latence proportionnelle à l’espace de base de données utilisé en raison de la copie des données</br>&bull; &nbsp;En règle générale, moins de 1 minute par Go d’espace utilisé|

> [!NOTE]
>
> - En cas de modification du niveau de service ou remise à l’échelle de calcul pour un pool élastique, la somme de l’espace utilisé sur toutes les bases de données dans le pool doit être utilisée pour calculer l’estimation.
> - Dans le cas de déplacement d’une base de données vers/à partir d’un pool élastique, uniquement l’espace utilisé par la base de données a un impact sur la latence, pas l’espace utilisé par le pool élastique.
>
> [!TIP]
> Pour surveiller des opérations en cours, voir : [Gérer des opérations à l’aide de l’API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gérer des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), [Surveiller des opérations à l’aide de T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) et les deux commandes PowerShell suivantes : [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) et [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considérations supplémentaires lors de la modification de taille de calcul de niveau ou la remise à l’échelle du service

- Lors de la baisse vCores ou Edtu pour un pool élastique, l’espace utilisé doit être inférieure à la taille maximale autorisée de la cible service niveau et pool d’Edtu.
- Lors de la remise à l’échelle vCores ou Edtu pour un pool élastique, un coût de stockage supplémentaire s’applique si (1) la taille maximale de stockage du pool est pris en charge par le pool cible, et (2) la taille maximale de stockage dépasse la quantité de stockage inclus du pool cible. Par exemple, si un pool Standard de 100 eDTU avec une taille maximale de 100 Go est rétrogradé en pool Standard de 50 eDTU, un coût de stockage supplémentaire s’applique, car le pool cible prend en charge une taille maximale de 100 Go et la quantité de stockage incluse est de seulement 50 Go. Par conséquent, la quantité de stockage supplémentaire est de 100 Go - 50 Go = 50 Go. Pour la tarification du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse.

### <a name="billing-during-rescaling"></a>Au cours de remise à l’échelle de facturation

Vous êtes facturé pour chaque heure d’existence de la base de données avec le niveau de service le plus élevé, la taille de calcul appliquée pendant cette heure quel que soit l’usage, ou si la base de données a été active pendant moins d’une heure. Par exemple, si vous avez créé une base de données unique et que vous l’avez supprimée cinq minutes après, votre facture mentionne le coût d’une heure de base de données.

## <a name="change-elastic-pool-storage-size"></a>modifier la taille de stockage de pool élastique

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

- Le stockage peut être approvisionné jusqu’à la limite de taille maximale :

  - Pour le stockage des niveaux de service Standard et Usage général, augmentez ou diminuez la taille par incrément de 10 Go.
  - Pour le stockage des niveaux de service Premium et Critique pour l’entreprise, augmentez ou diminuez la taille par incrément de 250 Go.
- Le stockage pour un pool élastique peut être configuré en augmentant ou diminuant sa taille maximale.
- Le prix du stockage pour un pool élastique est égal à la quantité de stockage multiplié par le prix unitaire du stockage pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

- Le prix des eDTU pour un pool élastique inclut une certaine quantité de stockage sans coût supplémentaire. Un espace de stockage en plus du volume inclus peut être approvisionné pour un coût supplémentaire jusqu’à la limite de taille par incréments de 250 Go jusqu’à 1 To, puis par incréments de 256 Go au-delà de 1 To. Pour les quantités de stockage et limites de taille maximale incluses, consultez [Pool élastique : tailles de stockage et tailles de calcul](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Vous pouvez configurer du stockage supplémentaire pour un pool élastique en augmentant sa taille maximale à l’aide du [portail Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), de [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [d’Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Le prix de l’espace de stockage supplémentaire pour un pool élastique est égal à la quantité de stockage supplémentaire multiplié par le prix unitaire du stockage supplémentaire pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Étapes suivantes

Pour les limites de ressources globales, consultez [SQL Database vCore-based resource limits - elastic pools](sql-database-vcore-resource-limits-elastic-pools.md) (limites de ressource vCore SQL Database - pools élastiques) et [SQL Database DTU-based resource limits - elastic pools](sql-database-dtu-resource-limits-elastic-pools.md) (limites de ressource DTU SQL Database - pools élastiques).
