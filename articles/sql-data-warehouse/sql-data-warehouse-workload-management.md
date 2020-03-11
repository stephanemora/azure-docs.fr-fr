---
title: Gestion des charges de travail
description: Conseils pour l’implémentation de la gestion des charges de travail dans Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14ea742a40afff8105560f1003655004687c7c9e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197655"
---
# <a name="what-is-workload-management"></a>Qu’est-ce que la gestion des charges de travail ?

L’exécution de charges de travail mixtes peut poser des problèmes de ressources sur les systèmes chargés.  Les architectes de solution cherchent des moyens de séparer les activités d’entreposage de données classiques (comme le chargement, la transformation et l’interrogation des données) pour s’assurer qu’il existe suffisamment de ressources pour répondre aux contrats SLA.  

L’isolation physique du serveur peut conduire à des poches d’infrastructure sous-exploitées, surutilisées ou dans un état où les caches sont constamment occupés avec le démarrage et l’arrêt du matériel.  Un schéma de gestion des charges de travail réussi gère efficacement les ressources, garantit une utilisation hautement efficace des ressources et optimise le retour sur investissement (ROI).

Le concept de « charge de travail d’entrepôt de données » fait référence à l’ensemble des opérations se produisant dans un entrepôt de données. La complexité et l’étendue de ces composants dépendent du niveau de maturité de l’entrepôt de données.  La charge de travail d’entrepôt de données englobe : 
- Le processus complet de chargement des données dans l’entrepôt 
- L’analyse et la création de rapports pour l’entrepôt de données
- La gestion des données dans l’entrepôt de données 
- L’exportation de données à partir de l’entrepôt de données

La capacité de performances d’un entrepôt de données est déterminée par les [unités de l’entrepôt de données](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Pour afficher les ressources allouées pour tous les profils de performances, voir [Limites de mémoire et de concurrence](memory-concurrency-limits.md).
- Pour ajuster la capacité, vous pouvez [l’augmenter ou la réduire](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Concepts de gestion des charges de travail
Dans le passé, pour SQL Analytics dans Azure Synapse, vous gériez les performances des requêtes par le biais de [classes de ressources](resource-classes-for-workload-management.md).  Les classes de ressources autorisaient l’attribution de mémoire à une requête en fonction de l’appartenance à un rôle.  La principale difficulté avec les classes de ressources est que, une fois celles-ci configurées, il n’existait aucune gouvernance ou possibilité de contrôler la charge de travail.  

Par exemple, l’octroi d’une appartenance à un rôle d’utilisateur ad hoc à smallrc permettait à cet utilisateur de consommer 100 % de la mémoire sur le système.  Avec les classes de ressources, il n’existe aucun moyen de réserver et de garantir la disponibilité des ressources pour les charges de travail critiques.

La gestion des charges de travail SQL Analytics se compose de trois concepts généraux : [La classification des charges de travail](sql-data-warehouse-workload-classification.md), [l’importance de la charge de travail](sql-data-warehouse-workload-importance.md) et [l’isolation de la charge de travail](sql-data-warehouse-workload-isolation.md).  Ces fonctionnalités vous permettent de mieux contrôler la façon dont votre charge de travail utilise les ressources système.

La classification des charges de travail est le concept d’affectation d’une requête à un groupe de charge de travail et de définition de niveaux d’importance.  Historiquement, cette affectation était effectuée via l’appartenance à un rôle à l’aide de [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Vous pouvez à présent utiliser le [CLASSIFIEUR CRÉER UNE CHARGE DE TRAVAIL](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  La fonctionnalité de classification fournit un ensemble plus riche d’options, avec les balises, les sessions et le temps nécessaire pour classer les requêtes.

L’importance de la charge de travail influence l’ordre dans lequel une requête accède aux ressources.  Sur un système occupé, une requête ayant une importance plus élevée a accès en premier aux ressources.  L’importance peut également garantir un accès ordonné aux verrous. 

L’isolation de la charge de travail réserve des ressources pour un groupe de charge de travail.  Les ressources réservées dans un groupe de charge de travail sont conservées exclusivement pour que le groupe de charge de travail puisse s’exécuter.  Les groupes de charges de travail vous permettent également de définir la quantité de ressources affectées par requête, de la même façon que les classes de ressources.  Les groupes de charges de travail vous permettent de réserver ou de limiter la quantité de ressources qu’un ensemble de requêtes peut consommer.  Enfin, les groupes de charge de travail sont un mécanisme permettant d’appliquer des règles, comme le délai de requête, aux requêtes.  


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la classification des charges de travail, consultez [Classification des charges de travail](sql-data-warehouse-workload-classification.md).  
- Pour plus d’informations sur l’isolation de la charge de travail, consultez [Isolation de la charge de travail](sql-data-warehouse-workload-isolation.md).  
- Pour plus d’informations sur l’importance de la charge de travail, consultez [Importance de la charge de travail](sql-data-warehouse-workload-importance.md).  
- Pour plus d’informations sur la surveillance de la gestion de la charge de travail, consultez [Supervision du portail de gestion des charges de travail](sql-data-warehouse-workload-management-portal-monitor.md).  
