---
title: Classification de l’entrepôt de données SQL | Microsoft Docs
description: Conseils d’utilisation de classification pour gérer l’accès concurrentiel, importance, ressources et de calcul pour les requêtes dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: c4630e11ef4001c546aecfe74dbced346b6d30e4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000116"
---
# <a name="sql-data-warehouse-workload-classification"></a>Classification de la charge de travail de SQL Data Warehouse

Cet article explique le processus de classification de la charge de travail de SQL Data Warehouse d’affectation d’une classe de ressources et l’importance aux requêtes entrantes.

## <a name="classification"></a>classification ;

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Classification de gestion de la charge de travail permet aux stratégies de la charge de travail à appliquer aux demandes par le biais de [classes de ressources](resource-classes-for-workload-management.md#what-are-resource-classes) et [importance](sql-data-warehouse-workload-importance.md).

Bien qu’il existe de nombreuses façons de classifier des charges de travail d’entreposage de données, la classification la plus simple et la plus courante est la charge et la requête. Vous chargez des données avec insert, update et des instructions de suppression.  Vous interrogez les données à l’aide des instructions SELECT. Une solution d’entrepôt de données ont souvent une stratégie de charge de travail pour l’activité de charge, tels que l’attribution d’une classe de ressources supérieure avec davantage de ressources. Une stratégie de charge de travail différents pourrait s’appliquent aux requêtes, telles qu’importance inférieure par rapport aux activités de charge.

Vous pouvez également subclassify vos charges de travail charge et de la requête. Sous-classification vous donne davantage de contrôle de vos charges de travail. Par exemple, les charges de travail de requête peuvent se composer d’actualisations de cube, les requêtes de tableau de bord ou les requêtes ad-hoc. Vous pouvez classer chacune de ces charges de travail de requête avec les classes de ressources différents ou des paramètres de l’importance. Charge peut également bénéficier de sous-classification. Transformations volumineuses peuvent être affectées à des classes de ressources plus grandes. Une importance plus élevée peut servir à garantir la clé données ventes chargeur avant les données météorologiques ou un flux de données sociales.

Pas toutes les instructions sont classées comme qu’ils n’exigent pas nécessitent des ressources ou avez besoin d’importance pour influencer l’exécution.  Les commandes DBCC, les instructions BEGIN, COMMIT et ROLLBACK TRANSACTION ne sont pas classées.

## <a name="classification-process"></a>Processus de classification

Classement dans SQL Data Warehouse s’effectue dès aujourd'hui en assignant des utilisateurs à un rôle qui a une classe de ressource correspondante qui lui est assignée à l’aide de [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). La possibilité pour caractériser les demandes au-delà d’une connexion à une classe de ressource est limitée avec cette fonctionnalité. Une méthode plus riche pour la classification est désormais disponible avec la [créer un classifieur de charge de travail](/sql/t-sql/statements/create-workload-classifier-transact-sql) syntaxe.  Avec cette syntaxe, les utilisateurs SQL Data Warehouse peuvent assigner importance et une classe de ressources aux requêtes.  

> [!NOTE]
> Classification est évaluée sur la base de chaque demande. Plusieurs demandes dans une seule session peuvent être classés différemment.

## <a name="classification-precedence"></a>Priorité de classement

Dans le cadre du processus de classification, la priorité est en place pour déterminer quelle classe de ressource est affectée. La classification basée sur un utilisateur de base de données est prioritaire sur l’appartenance au rôle. Si vous créez un classifieur qui mappe l’utilisateur de base de données UserA à la classe de ressources mediumrc. Ensuite, mappez le rôle de base de données RoleA (dont l’utilisateur a est membre) à la classe de ressources largerc. Le classifieur qui mappe l’utilisateur de base de données à la classe de ressources mediumrc ont priorité sur le classifieur qui mappe le rôle de base de données RoleA à la classe de ressources largerc.

Si un utilisateur est membre de plusieurs rôles avec les classes de ressources différent affecté ou mis en correspondance dans plusieurs classifieurs, l’utilisateur bénéficie de l’attribution de classe de ressource la plus élevée.  Ce comportement est cohérent avec le comportement d’attribution de classe ressource existante.

## <a name="system-classifiers"></a>Classifieurs de système

Classification de la charge de travail a des classifieurs de charge de travail système. Les classifieurs de système de mappage existant appartenances de rôle de classe de ressource pour les allocations de ressources de classe de ressource importance normale. Impossible de supprimer les classifieurs de système. Pour afficher les classifieurs de système, vous pouvez exécuter la sous requête :

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Mélange de ressources des affectations de classe avec des classifieurs

Les classifieurs système créées en votre nom permettent un accès facile pour migrer vers la classification de la charge de travail. À l’aide des mappages de rôle de classe de ressource avec la priorité de classement, peut entraîner classification incorrecte lorsque vous commencez à créer des classifieurs de nouveau avec une importance.

Examinez le scénario suivant :

- Un entrepôt de données a un utilisateur de base de données que dbauser attribué au rôle de classe de ressources largerc. L’attribution de classe de ressource a été effectuée avec sp_addrolemember.
- L’entrepôt de données est désormais mis à jour avec gestion de la charge de travail.
- Pour tester la nouvelle syntaxe de classification, le rôle de base de données DBARole (c'est-à-dire DBAUser membre), a un classifieur créé pour lui mappant à mediumrc et une importance haute.
- Lorsque DBAUser se connecte et exécute une requête, la requête est assignée à largerc. Étant donné que l’utilisateur est prioritaire sur une appartenance au rôle.

Pour simplifier la résolution des problèmes de classification incorrecte, nous vous recommandons de que supprimer mappages de rôle de classe de ressource que vous créez les classifieurs de charge de travail.  Le code ci-dessous retourne des appartenances aux rôles de classe ressource existante.  Exécutez [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) pour chaque nom de membre retournée à partir de la classe de ressource correspondante.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la création d’un classifieur, consultez le [créer le classifieur de charge de travail (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Consultez le Guide de démarrage rapide sur la création d’un classifieur de charge de travail [créer un classifieur de charge de travail](quickstart-create-a-workload-classifier-tsql.md).
- Consultez les articles de savoir-faire pour [Importance de la charge de travail configurer](sql-data-warehouse-how-to-configure-workload-importance.md) et comment [gérer et surveiller la gestion de la charge de travail](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultez [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) pour voir les requêtes et l’importance attribuée.