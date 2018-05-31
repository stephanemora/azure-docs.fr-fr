---
title: Mettre à niveau vers la dernière génération d’Azure SQL Data Warehouse | Microsoft Docs
description: Mettez à niveau Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 58d65ef05ed872bb357070de9866253baea5dc70
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777805"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimiser les performances en mettant à niveau SQL Data Warehouse
Mettez à niveau Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure.

## <a name="why-upgrade"></a>Pourquoi procéder à une mise à niveau ?
La mise à niveau vers la deuxième génération de SQL Data Warehouse se fait maintenant en toute transparence sur le Portail Azure. Si votre entrepôt de données est de la première génération, il est recommandé de le mettre à niveau. En effectuant la mise à niveau, vous pouvez utiliser la dernière génération de matériel et d’architecture de stockage améliorée Azure. Vous pouvez ainsi bénéficier de meilleures performances, d’une scalabilité supérieure et d’un stockage en colonnes illimité. 

## <a name="applies-to"></a>S’applique à
Cette mise à niveau s’applique aux entrepôts de données de première génération.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Avant de commencer
> [!NOTE]
> Si votre entrepôt de données de première génération existant ne se trouve pas dans l’une des régions dans lesquelles la deuxième génération est disponible, vous pouvez effectuer une [géo-restauration vers la deuxième génération](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) avec PowerShell dans une région prise en charge.
> 
>

1. Si l’entrepôt de données de première génération à mettre à niveau est en pause, [reprenez son exécution](pause-and-resume-compute-portal.md).
2. Attendez-vous à quelques minutes de temps d’arrêt. 



## <a name="start-the-upgrade"></a>Lancer la mise à niveau

1. Ouvrez votre entrepôt de données de première génération sur le Portail Azure, puis cliquez sur **Mettre à niveau vers la deuxième génération** : ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Par défaut, **sélectionnez le niveau de performance suggéré** pour l’entrepôt de données en fonction de votre niveau actuel pour la première génération en suivant les correspondances ci-dessous :
    
| Première génération | Deuxième génération |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Vérifiez que l’exécution de votre charge de travail est terminée et arrêtée avant de mettre à niveau. Votre entrepôt de données sera opérationnel en tant qu’entrepôt de données de deuxième génération après un temps d’arrêt de quelques minutes. **Cliquez sur Mettre à niveau**. Le prix du niveau de performance de deuxième génération est divisé par deux pendant la période de préversion :
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Surveillez votre mise à niveau** en vérifiant l’état dans le portail Azure :

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   La première étape du processus de mise à niveau passe par l’opération de mise à l’échelle (« Mise à niveau - Hors connexion ») pendant laquelle toutes les sessions seront supprimées et les connexions fermées. 
   
   La deuxième étape du processus de mise à niveau est la migration des données (« Mise à niveau - En ligne »). La migration des données est un processus en arrière-plan progressif en ligne qui déplace lentement les données en colonnes de l’ancienne architecture de stockage vers la nouvelle architecture de stockage, en tirant parti du cache de disque SSD local. Pendant ce temps, votre entrepôt de données sera en ligne à des fins d’interrogation et de chargement. Toutes vos données pourront être interrogées, qu’elles aient été migrées ou non. La migration des données se produit à un taux variable selon la taille de vos données, de votre niveau de performance et du nombre de vos segments de columnstore. 

5. **Trouvez votre entrepôt de données de deuxième génération** dans le panneau de navigation des bases de données SQL. 

> [!NOTE]
> À l’heure actuelle, il peut arriver que les entrepôts de données de deuxième génération n’apparaissent pas dans le panneau de navigation des entrepôts de données SQL. Veuillez utiliser le panneau de navigation des bases de données SQL pour trouver votre entrepôt de données de deuxième génération mis à niveau. Nous travaillons activement à résoudre ce problème.
> 

6. **Recommandation facultative :** pour accélérer le processus de migration en arrière-plan, il est recommandé de forcer immédiatement le déplacement des données en exécutant la commande [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) sur toutes les tables de columnstore sur une plus large classe de SLO et ressources. Cette opération, par rapport au processus en arrière-plan progressif, est en mode hors connexion. Toutefois, la migration des données sera beaucoup plus rapide et, une fois terminée, vous pourrez tirer pleinement parti de la nouvelle architecture de stockage améliorée avec des groupes de lignes de haute qualité. 

La requête suivante génère les commandes Alter Index Rebuild requises pour accélérer le processus de migration des données :

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Étapes suivantes
Votre entrepôt de données mis à niveau est en ligne. Pour tirer parti de l’architecture améliorée, consultez [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md).
 
