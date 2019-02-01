---
title: Mettre à niveau vers la dernière génération d’Azure SQL Data Warehouse | Microsoft Docs
description: Mettez à niveau Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 173846e4828228bdc51fc42858e0c6c9b00cafd6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242788"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimiser les performances en mettant à niveau SQL Data Warehouse
Mettez à niveau Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure.

## <a name="why-upgrade"></a>Pourquoi procéder à une mise à niveau ?
Vous pouvez maintenant effectuer une mise à niveau de manière fluide vers le niveau Gen2 optimisé pour le calcul d’Azure SQL Data Warehouse dans le portail Azure. Si vous avez un entrepôt de données de niveau Gen1 optimisé pour le calcul, la mise à niveau est recommandée. En effectuant la mise à niveau, vous pouvez utiliser la dernière génération de matériel et d’architecture de stockage améliorée Azure. Vous pouvez ainsi bénéficier de meilleures performances, d’une scalabilité supérieure et d’un stockage en colonnes illimité. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>S’applique à
Cette mise à niveau s’applique aux entrepôts de données de niveau Gen1 optimisé pour le calcul.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Avant de commencer
> [!NOTE]
> Si votre entrepôt de données de niveau Gen1 optimisé pour le calcul existant ne se trouve pas dans une région où le niveau Gen2 optimisé pour le calcul est disponible, vous pouvez effectuer une [géo-restauration](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) par le biais de PowerShell dans une région prise en charge.
>
> 

1. Si l’entrepôt de données de niveau Gen1 optimisé pour le calcul à mettre à niveau est suspendu, [reprenez l’exécution de l’entrepôt de données](pause-and-resume-compute-portal.md).

2. Attendez-vous à quelques minutes de temps d’arrêt. 

3. Identifiez les références de code à des niveaux de performances Gen1 optimisé pour le calcul et modifiez-les à leur niveau de performances Gen2 optimisé pour le calcul équivalent. Les deux exemples ci-dessous illustrent où vous devez mettre à jour les références de code avant la mise à niveau :

   Commande PowerShell Gen1 d’origine :

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Remplacée par :

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" est remplacé par - RequestedServiceObjectiveName "DW300**c**"
   >

   Commande T-SQL Gen1 d’origine :

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Remplacée par :

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
    > [!NOTE] 
    > SERVICE_OBJECTIVE = 'DW300' est remplacé par SERVICE_OBJECTIVE = 'DW300**c**'



## <a name="start-the-upgrade"></a>Lancer la mise à niveau

1. Ouvrez votre entrepôt de données de niveau Gen1 optimisé pour le calcul dans le portail Azure, puis cliquez dans l’encadré **Mettre à niveau vers Gen2** sous l’onglet Tâches :  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Si vous ne voyez pas la carte **Mettre à niveau vers la 2e génération** sous l’onglet Tâches, votre type d’abonnement est limité dans la région actuelle.
    > [Envoyez un ticket de support](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) pour mettre votre abonnement sur liste verte.

2. Par défaut, **sélectionnez le niveau de performances suggéré** pour l’entrepôt de données en fonction de votre niveau de performances actuel sur le niveau Gen1 optimisé pour le calcul en utilisant le mappage ci-dessous :

   | Niveau Gen1 optimisé pour le calcul | Niveau Gen2 optimisé pour le calcul |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

3. Vérifiez que l’exécution de votre charge de travail est terminée et arrêtée avant de mettre à niveau. Vous subirez un temps d’arrêt de quelques minutes avant la remise en ligne de votre entrepôt de données comme entrepôt de données de niveau Gen2 optimisé pour le calcul. **Cliquez sur Mettre à niveau** :

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Surveillez votre mise à niveau** en vérifiant l’état dans le portail Azure :

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   La première étape du processus de mise à niveau passe par l’opération de mise à l’échelle (« Mise à niveau - Hors connexion ») pendant laquelle toutes les sessions seront supprimées et les connexions fermées. 

   La deuxième étape du processus de mise à niveau est la migration des données (« Mise à niveau - En ligne »). La migration des données est un processus en arrière-plan progressif en ligne qui déplace lentement les données en colonnes de l’ancienne architecture de stockage vers la nouvelle architecture de stockage, en tirant parti du cache de disque SSD local. Pendant ce temps, votre entrepôt de données sera en ligne à des fins d’interrogation et de chargement. Toutes vos données pourront être interrogées, qu’elles aient été migrées ou non. La migration des données se produit à un taux variable selon la taille de vos données, de votre niveau de performance et du nombre de vos segments de columnstore. 

5. **Recommandation facultative :** pour accélérer le processus de migration de données en arrière-plan, vous pouvez forcer immédiatement le déplacement des données en exécutant la commande [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) sur toutes les tables columnstore primaires que vous interrogez sur une plus large classe de ressources et SLO. Cette opération, par rapport au processus en arrière-plan progressif dont l’exécution peut prendre plusieurs heures en fonction du nombre et de la taille de vos tables, est en mode **hors connexion**. Toutefois, la migration des données sera beaucoup plus rapide et, une fois terminée, vous pourrez tirer pleinement parti de la nouvelle architecture de stockage améliorée avec des groupes de lignes de haute qualité. 

La requête suivante génère les commandes Alter Index Rebuild exigées pour accélérer le processus de migration des données :

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
