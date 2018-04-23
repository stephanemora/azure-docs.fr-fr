---
title: Mettre à niveau vers la dernière génération d’Azure SQL Data Warehouse | Microsoft Docs
description: Étapes de la mise à niveau d’Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6ea45398b0bf7fca43c75797313b7e683972b1ab
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimiser les performances en mettant à niveau SQL Data Warehouse

Vous pouvez maintenant mettre à niveau en toute transparence vers un niveau de performance optimisé pour le calcul dans le portail Azure. Si vous avez un entrepôt de données optimisé pour l’élasticité, nous vous recommandons de mettre à niveau vers la dernière génération de matériel Azure et une architecture de stockage améliorée. Vous pourrez ainsi bénéficier de meilleures performances, d’une extensibilité supérieure et d’un stockage en colonnes illimité. 

## <a name="applies-to"></a>S’applique à
Cette mise à niveau s’applique aux entrepôts de données du niveau de performances Optimisé pour l’élasticité.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Avant de commencer

> [!NOTE]
> Depuis le 30/03, l’[audit au niveau serveur](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing#subheading-8) doit être désactivé avant de commencer la mise à niveau.
> 
>

> [!NOTE]
> Si votre entrepôt de données optimisé pour l’élasticité existant ne se trouve pas dans une région où Optimisé pour le calcul est disponible, vous pouvez [géo-restaurer pour Optimisé pour le calcul](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) via PowerShell vers une région prise en charge.
> 
>

1. Si l’entrepôt de données Optimisé pour l’élasticité à mettre à niveau est mis en pause, [reprenez l’exécution de l’entrepôt de données](pause-and-resume-compute-portal.md).
2. Attendez-vous à quelques minutes de temps d’arrêt. 



## <a name="start-the-upgrade"></a>Lancer la mise à niveau

1. Accédez à votre entrepôt de données optimisé pour l’élasticité dans le portail Azure, puis cliquez sur **Mettre à niveau vers Optimisé pour le calcul** :  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Par défaut, **sélectionnez le niveau de performance suggéré** pour l’entrepôt de données selon votre niveau de performance actuel sur Optimisé pour l'élasticité en utilisant le mappage ci-dessous :
    
| Optimisé pour l’élasticité | Optimisé pour le calcul |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Vérifiez que l’exécution de votre charge de travail est terminée et arrêtée avant de mettre à niveau. Vous rencontrerez un temps d’arrêt de quelques minutes avant la remise en ligne de votre entrepôt de données en tant qu’entrepôt de données optimisé pour le calcul. **Cliquez sur Mettre à niveau**. Le prix du niveau de performance Optimisé pour le calcul est actuellement divisé de moitié pendant la période d’évaluation :
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Surveillez votre mise à niveau** en vérifiant l’état dans le portail Azure :

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   La première étape du processus de mise à niveau passe par l’opération de mise à l’échelle (« Mise à niveau - Hors connexion ») pendant laquelle toutes les sessions seront supprimées et les connexions fermées. 
   
   La deuxième étape du processus de mise à niveau est la migration des données (« Mise à niveau - En ligne »). La migration des données est un processus en arrière-plan progressif en ligne qui déplace lentement les données en colonnes de l’ancienne architecture de stockage de génération 1 vers la nouvelle architecture de stockage de génération 2 pour tirer parti du cache de disque SSD local de génération 2. Pendant ce temps, votre entrepôt de données sera en ligne à des fins d’interrogation et de chargement. Toutes vos données pourront être interrogées, qu’elles aient été migrées ou non. La migration des données se produit à un taux variable selon la taille de vos données, de votre niveau de performance et du nombre de vos segments de columnstore. 

5. **Recommandation facultative :** pour accélérer le processus de migration en arrière-plan, il est recommandé de forcer immédiatement le déplacement des données en exécutant la commande [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index) sur toutes les tables de columnstore sur une plus large classe de SLO et ressources. Cette opération, par rapport au processus en arrière-plan progressif, est en mode hors connexion. Toutefois, la migration des données sera beaucoup plus rapide et, une fois terminée, vous pourrez tirer pleinement parti de l’architecture de stockage de génération 2 avec des groupes de lignes de haute qualité. 

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
 
