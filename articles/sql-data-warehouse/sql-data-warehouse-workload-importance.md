---
title: Importance de la charge de travail | Microsoft Docs
description: Conseils pour la définition d’importance pour les requêtes dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 92990b68969e754ee126b6cd5a22ecfa700c0494
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002887"
---
# <a name="sql-data-warehouse-workload-importance"></a>Importance de la charge de travail de SQL Data Warehouse

Cet article explique comment importance de la charge de travail peut influencer l’ordre d’exécution pour les requêtes SQL Data Warehouse.

## <a name="importance"></a>Importance 

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Besoins de l’entreprise peuvent nécessiter pour être plus importante que d’autres charges de travail d’entreposage de données.  Imaginez un scénario dans lequel les données de ventes critiques sont chargées avant l’exercice de clôture de période.  Données sont chargées pour d’autres sources telles que les données météorologiques inutile SLA stricts.   Paramètre de haute importance pour une demande de chargement des données de ventes et faible importance à une demande de chargement si les données garantit le chargement des données de ventes Obtient le premier accès aux ressources et se termine plus rapidement.

## <a name="importance-levels"></a>Niveaux d’importance

Il existe cinq niveaux d’importance : faible, below_normal, normal, above_normal et haute.  Les demandes qui ne définissez importance sont affectées le niveau par défaut de normal.  Les demandes qui ont le même niveau d’importance ont le même comportement de planification qui existe aujourd'hui.

## <a name="importance-scenarios"></a>Scénarios d’importance

Au-delà du scénario de base d’importance décrit ci-dessus avec les ventes et les données météorologiques, voici les autres scénarios où importance de la charge de travail vous aide à répondre à traitement des données et l’interrogation des besoins.

### <a name="locking"></a>Verrouillage

Accès aux verrous de lecture et d’écriture activité est une zone de contention naturelle.  Activités telles que [basculement de partition](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) ou [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql) requièrent des verrous avec élévation de privilèges.  Sans importance de la charge de travail, SQL Data Warehouse optimise pour le débit.  Optimisation de débit signifie que lors de l’exécution et de demandes en file d’attente ont les mêmes besoins de verrouillage et de ressources sont disponibles, les demandes en file d’attente peuvent contourner les demandes avec des besoins de verrouillage plus élevé qui est arrivé dans la file d’attente de demande précédemment.  Une fois l’importance de la charge de travail est appliqué aux demandes avec verrouillage plus élevée doit. Demande une importance plus élevée est exécuté avant la demande avec une importance inférieure.

Prenons l'exemple suivant :

Q1 est en cours d’exécution et sélection des données à partir de SalesFact activement.
2e trimestre est en file d’attente en attente pour Q1 terminer.  Il a été envoyé à 9 h 00 et tente de nouvelles données de partition commutateur dans SalesFact.
Q3 est envoyé à 9 h 01 et souhaite sélectionner des données à partir de SalesFact.

Si Q2 et Q3 ont la même importance et Q1 est en cours d’exécution, Q3 commence l’exécution. Q2 doit continuer à attendre un verrou exclusif sur SalesFact.  Si Q2 a une importance plus élevée que Q3, Q3 attend jusqu'à ce que Q2 est terminée avant de commencer l’exécution.

### <a name="non-uniform-requests"></a>Demandes non uniforme

Un autre scénario où importance peut vous aider à répondre aux demandes d’interrogation est lorsque les demandes avec des classes de ressources différents sont envoyés.  Comme mentionné précédemment, sous la même importance, SQL Data Warehouse optimise le débit.  Lorsque des demandes de taille mixte (par exemple, smallrc ou mediumrc) sont en attente, SQL Data Warehouse choisira la demande plus ancien entrant qui s’intègre au sein des ressources disponibles.  Si l’importance de la charge de travail est appliqué, la demande d’importance plus élevée est ensuite planifiée.
  
Prenons l’exemple suivant sur DW500c :

Q1, Q2, 3e et 4e trimestre sont en cours d’exécution des requêtes smallrc.
Q5 est envoyé avec la classe de ressources mediumrc à 9 h 00.
Q6 est envoyé avec la classe de ressources smallrc à 9 h 01.

Q5 étant mediumrc, elle nécessite deux emplacements de concurrence.  Q5 doit attendre pour deux des requêtes en cours d’exécution pour terminer.  Toutefois, lorsqu’une des requêtes en cours d’exécution (Q1 Q4) se termine, Q6 est planifiée immédiatement, car les ressources existent pour exécuter la requête.  Si Q5 a une importance plus élevée que Q6, Q6 attend jusqu'à ce que Q5 est en cours d’exécution avant de commencer l’exécution.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la création d’un classifieur, consultez le [créer le classifieur de charge de travail (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Pour plus d’informations sur la classification de la charge de travail de SQL Data Warehouse, consultez [Classification de la charge de travail](sql-data-warehouse-workload-classification.md).  
- Consultez le Guide de démarrage rapide [créer classifieur de charge de travail](quickstart-create-a-workload-classifier-tsql.md) pour savoir comment créer un classifieur de charge de travail.
- Consultez les articles de savoir-faire pour [Importance de la charge de travail configurer](sql-data-warehouse-how-to-configure-workload-importance.md) et comment [gérer et surveiller la charge de travail](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultez [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) pour voir les requêtes et l’importance attribuée.
