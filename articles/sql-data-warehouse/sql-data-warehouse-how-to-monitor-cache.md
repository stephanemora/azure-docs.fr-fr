---
title: Optimiser votre cache Gen2 | Microsoft Docs
description: Découvrez comment surveiller votre cache Gen2 à l’aide du portail Microsoft Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.component: performance
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2a0504ae0e5c3dbf70ad84526176beae52f55870
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103126"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Procédure de surveillance du cache Gen2
L’architecture de stockage Gen2 nivelle automatiquement vos segments columnstore les plus fréquemment interrogés dans un cache résidant sur des disques SSD basés sur NVMe conçus pour les entrepôts de données Gen2. Les performances sont optimisées lorsque vos requêtes récupèrent des segments qui résident dans le cache. Cet article décrit la procédure de surveillance et de résolution des problèmes de lenteur des performances des requêtes en déterminant si votre charge de travail exploite lu cache Gen2 de façon optimale.  
## <a name="troubleshoot-using-the-azure-portal"></a>Résolution des problèmes à l’aide du portail Azure
Vous pouvez utiliser Azure Monitor pour afficher les mesures du cache Gen2 afin de résoudre les problèmes de performances des requêtes. Commencez par accéder au portail Microsoft Azure et cliquez sur Monitor :

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Sélectionnez le bouton Métriques et renseignez **Abonnement**, **Groupe de** **ressources**, **Type de ressource** et **Nom de la ressource** pour votre entrepôt de données.

Les métriques clés pour la résolution des problèmes du cache Gen2 sont **Pourcentage d’accès au cache** et **Pourcentage de cache utilisé**. Configurez le graphique de métrique Azure pour afficher ces deux métriques.

![Métriques du cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Correspondance dans le cache et pourcentage utilisé
La matrice ci-dessous décrit les scénarios en fonction des valeurs des métriques de cache :

|                                | **Pourcentage d’accès au cache élevé** | **Pourcentage d’accès au cache faible** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Pourcentage de cache utilisé élevé** |          Scénario 1           |          Scénario 2          |
| **Pourcentage de cache utilisé faible**  |          Scénario 3           |          Scénario 4          |

**Scénario 1 :** Vous utilisez votre cache de manière optimale. [Résolvez les problèmes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) d’autres éléments qui peuvent ralentir vos requêtes.

**Scénario 2 :** Votre jeu de données de travail actuel ne peut pas tenir dans le cache, ce qui provoque un faible pourcentage d’accès au cache en raison des lectures physiques. Tenez compte de la montée en puissance de votre niveau de performance, puis réexécutez votre charge de travail pour remplir le cache.

**Scénario 3 :** Il est probable que votre requête s’exécute lentement pour des raisons non liées au cache. [Résolvez les problèmes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) d’autres éléments qui peuvent ralentir vos requêtes. Vous pouvez également envisager une [descente en puissance de votre instance](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) pour réduire la taille du cache afin de diminuer les coûts. 

**Scénario 4 :** Vous aviez un cache froid, ce qui est peut-être la raison pour laquelle votre requête a été lente. Envisagez de réexécuter votre requête, car votre jeu de données de travail doit maintenant être mis en cache. 

**Important : Si le pourcentage d’accès au cache ou le pourcentage de cache utilisé n’est pas mis à jour après une réexécution de votre charge de travail, votre plage de travail réside peut-être déjà en mémoire. Notez que seules les tables columnstore du cluster sont mises en cache.**

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le réglage des performances des requêtes générales, consultez [Surveillance de l’exécution des rêquêtes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
