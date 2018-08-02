---
title: Notes de publication Azure SQL Data Warehouse juin 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216707"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Nouveautés dans Azure SQL Data Warehouse Juillet 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en juillet 2018.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Granularité plus fine pour les restaurations inter-serveur et inter-région
Vous pouvez maintenant effectuer des restaurations entre les régions et les serveurs à l’aide de n’importe quel point de restauration au lieu de sélectionner des sauvegardes géo-redondantes effectuées toutes les 24 heures. Les restaurations inter-serveur et inter-région sont prises en charge pour les points de restauration automatiques ou définis par l’utilisateur et offrent une granularité plus fine pour une protection des données renforcée. Davantage de points de restauration sont disponibles, ce qui garantit que votre entrepôt de données sera logiquement cohérent lors d’une restauration dans différentes régions.

![Commande Restore - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Options de restauration - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Pour plus d’informations, consultez le billet de blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Points de restauration flexibles et accélérés).

## <a name="20-minute-restorations"></a>Restaurations de 20 minutes
SQL Data Warehouse permet désormais de restaurer n’importe quel entrepôt de données en **moins de 20 minutes** dans la même région, quelle que soit la taille de la base de données. Le délai de restauration s’applique que la restauration se fasse sur un serveur logique identique ou distinct dans la même région. En outre, le processus de capture instantanée a été amélioré pour réduire le délai de création d’un point de restauration. Pour les niveaux de performance inférieurs (paramètres DWU inférieurs), le délai de création d’instantané a été *divisé par 2*.

Pour plus d’informations, consultez le billet de blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Points de restauration flexibles et accélérés).

## <a name="custom-restoration-configurations"></a>Configurations de restauration personnalisées
Vous pouvez désormais modifier votre niveau de performance (DWU) lors d’une restauration dans le portail Azure. Vous pouvez également effectuer une restauration vers un entrepôt de données Gen2 mis à niveau. En effectuant une restauration vers une instance de deuxième génération, vous pouvez évaluer l’impact d’une telle instance avant de [mettre à niveau votre entrepôt de données Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configuration de restauration personnalisée - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
La procédure stockée [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) est souvent utilisée par les outils pour obtenir des métadonnées sur les paramètres dans un lot Transact-SQL. La procédure renvoie une ligne pour chaque paramètre du lot avec les informations de type déduites pour ce paramètre. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Le jeu de résultats inclut des métadonnées sur le paramètre `@id` (résultats partiels présentés)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```