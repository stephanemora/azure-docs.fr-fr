---
title: Résoudre les problèmes de charge de travail mal classée dans un pool de SQL dédié
description: Identifiez et résolvez les problèmes des scénarios dans lesquels les charges de travail sont mal classées dans des groupes de charges de travail inattendus dans un pool SQL dédié dans Azure Synapse Analytics.
author: SudhirRaparla
ms.author: nvraparl
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 10/01/2021
ms.custom: template-how-to
ms.reviewer: wiassaf
ms.openlocfilehash: 1fa0394f3e711ec172f5a3ad32288f63bcf55dce
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390618"
---
# <a name="troubleshooting-a-misclassified-workload-in-azure-synapse-analytics"></a>Résolution des problèmes d’une charge de travail mal classée dans Azure Synapse Analytics

Cet article fournit des instructions sur la résolution de problèmes relatifs à une charge de travail mal classée et sur l’identification de la raison de la classification pour les charges de travail d’un pool SQL dédié.

Azure Synapse Analytics offre des fonctionnalités de gestion de la charge de travail, telles que la [classification des charges de travail en groupes de charge de travail appropriés](sql-data-warehouse-workload-classification.md), l’[affectation de l'importance](sql-data-warehouse-workload-importance.md) et l'[isolement des ressources](sql-data-warehouse-workload-isolation.md) pour respecter les contrats SLA. 

Toutefois, dans certains scénarios, une combinaison de ces fonctionnalités peut aboutir à une classification de charge de travail qui ne reflète pas l’intention de l’utilisateur. Cet article répertorie les scénarios courants et les procédures de résolution des problèmes. Tout d’abord, vous devez interroger les informations de base pour résoudre les problèmes de scénarios de charge de travail mal classés.

> [!NOTE]
> Cet article ne s’applique pas aux pools SQL serverless d’Azure Synapse Analytics.

## <a name="basic-troubleshooting-information"></a>Informations de base sur la résolution des problèmes

Pour résoudre les problèmes liés à un scénario de charge de travail mal classée, les informations suivantes sont nécessaires :

- Liste des [groupes de charges de travail](#workload-groups)
- Liste de tous les [classifieurs de charge de travail](#workload-classifiers) et des groupes de charges de travail associés
- Liste des [groupes d’utilisateurs et de charges de travail mappés](#users-and-mapped-resource-classes) (définis par le système et par l’utilisateur) 
- Liste des [détails sur les classifieurs et les groupes de charges de travail](#workload-group-and-classifier-details-of-a-request) d’une requête


### <a name="workload-groups"></a>Groupes de charges de travail

#### <a name="azure-portal"></a>Portail Azure

Pour obtenir la liste de tous les groupes de charges de travail (notamment les groupes de charges de travail système) et les détails associés dans le portail Azure :

1. Accédez à l’espace de travail Azure Synapse sous lequel le pool SQL dédié concerné a été créé.
1. Le volet gauche répertorie tous les types de pools créés sous l’espace de travail. Sélectionnez **Pools SQL** dans la section **Pools analytiques**.
1. Sélectionnez le pool SQL dédié concerné.
1. Dans le volet gauche, sélectionnez **Gestion de la charge de travail** sous **Paramètres**.
1. La section **Groupes de charges de travail** répertorie toutes les charges de travail. Par défaut, seuls les **groupes de charges de travail définis par l’utilisateur** sont répertoriés. Pour afficher la liste des groupes de charges de travail définis par l’utilisateur et définis par le système, modifiez le filtre et **Sélectionnez tout**.

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png" alt-text="Filtre de liste de groupes de charges de travail" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png":::

#### <a name="t-sql"></a>T-SQL

Pour afficher les groupes de charges de travail à l’aide de T-SQL, [connectez-vous au pool SQL dédié à l’aide de SQL Server Management Studio (SSMS)](../sql/get-started-ssms.md) et exécutez la requête suivante :
 
```sql
SELECT * FROM sys.workload_management_workload_groups;
```

Pour plus d’informations, consultez [sys.workload_management_workload_groups](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql).


### <a name="workload-classifiers"></a>Classifieurs de charge de travail

#### <a name="azure-portal"></a>Portail Azure

Pour répertorier tous les classifieurs de charge de travail (notamment les classifieurs définis par le système) par groupe de charges de travail dans le portail Azure, consultez les numéros répertoriés dans la colonne **Classifieurs** dans la table des groupes de charges de travail (voir la section précédente).

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png" alt-text="Liste des classifieurs de groupe de charges de travail" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png":::

#### <a name="t-sql"></a>T-SQL

Pour obtenir la liste de tous les classifieurs de charge de travail (notamment les classifieurs définis par le système) à l’aide de T-SQL, utilisez la requête suivante :

```sql
SELECT * FROM sys.workload_management_workload_classifiers;
```

Pour plus d’informations, consultez [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql).

### <a name="users-and-mapped-resource-classes"></a>Utilisateurs et classes de ressources mappées

Pour obtenir la liste des classes de ressources définies par le système et des utilisateurs mappés dans T-SQL, utilisez la requête suivante :

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS [Member Name]
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');
```

### <a name="workload-group-and-classifier-details-of-a-request"></a>Détails sur les classifieurs et les groupes de charges de travail d’une requête

La première étape de la résolution des problèmes de charge de travail mal classée consiste à identifier le groupe de charges de travail et le classifieur de charge de travail pour une requête. Utilisez la vue de gestion dynamique [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) pour afficher les requêtes soumises et leur classification :

```sql
SELECT * FROM sys.dm_pdw_exec_requests;
```

Pour plus d’informations, consultez [sys.dm_pdw_exec_requests](/sql/relational-databases/system-catalog-views/sys-dm-pdw-exec-requests-transact-sql).

## <a name="common-scenarios-of-misclassified-workloads"></a>Scénarios courants de charges de travail mal classées

Voici quelques scénarios courants dans lesquels des charges de travail peuvent involontairement être mal classées :

### <a name="mixed-usage-of-resource-classes-and-user-defined-workload-management"></a>Utilisation mixte des classes de ressources et de la gestion des charges de travail définies par l’utilisateur

Dans les scénarios où les classes de ressources et les groupes de charges de travail sont utilisés ensemble, le rôle d’utilisateur pour les mappages de classes de ressources peut entrer en conflit avec les règles du classifieur de charge de travail et entraîner une classification de requête involontaire. 

Considérons le scénario suivant :

1. Un utilisateur de base de données, DBAUser, est affecté au rôle de classe de ressources largerc à l’aide de la procédure `sp_addrolemember`.
1. DBAUser a créé un groupe de charges de travail et un classifieur à l’aide de la gestion des charges de travail.
1. Un classificateur de charge de travail créé récemment mappe le rôle de base de données DBARole à la classe de ressources mediumrc avec une importance élevée. 
1. DBAUser est un membre du rôle de base de données DBARole.
1. Lorsque DBAUser exécute une requête, celle-ci est supposée s’exécuter sur mediumrc en fonction du classificateur de charge de travail. Au lieu de cela, elle est assignée à largerc, car le mappage de l'**utilisateur** est prioritaire sur le mappage de l’**appartenance d’un rôle** à un classifieur.

Il est préférable d’éviter de mélanger l’utilisation des classes de ressources et des groupes de gestion des charges de travail pour gérer la charge de travail. Pour plus d’informations sur les étapes de conversion des classes de ressources en charges de travail, consultez [Convertir des classes de ressources en groupes de charges de travail](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md). 

Toutefois, dans certaines situations, les classes de ressources et la gestion des charges de travail doivent être utilisées ensemble. Dans certains scénarios, pour simplifier la résolution des problèmes de classification, nous vous recommandons de supprimer les mappages de rôle de classe de ressources, car vous créez des classifieurs de charge de travail. Le code ci-dessous retourne des appartenances existantes aux rôles de classe de ressources. Exécutez [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) pour chaque nom de membre retourné par la classe de ressource correspondante.

```sql
SELECT  r.name AS [Resource Class]
, m.name AS membername
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
EXEC sp_droprolemember '[Resource Class]', membername;
```

### <a name="some-administrative-users-are-always-mapped-to-smallrc-workload-group"></a>Certains utilisateurs administratifs sont toujours mappés au groupe de charges de travail smallrc

Considérons un scénario pour la connexion d’administrateur SQL de l’espace de travail Azure Synapse, l’administrateur Azure Synapse Azure Active Directory (utilisateur ou membre de groupe) ou un propriétaire de base de données. Ces utilisateurs peuvent toujours avoir un classifieur de charge de travail ou ont été ajoutés à un rôle de classe de ressource autre que smallrc. Toutes les requêtes exécutées par ces utilisateurs continueront à s’exécuter sur la classe de ressources smallrc, même si l’utilisateur est mappé à une classe de ressources ou un groupe de charges de travail différent. 

**Recommandation** : ces utilisateurs administratifs ne peuvent pas modifier leur groupe de charges de travail par défaut. Pour plus d’informations, consultez la [gestion des charges de travail avec des classes de ressources](resource-classes-for-workload-management.md#default-resource-class). Il est recommandé de ne pas exécuter des charges de travail critiques ou sensibles aux performances en tant qu’un de ces utilisateurs administratifs dans le pool SQL dédié.

La connexion d’administrateur SQL de l’espace de travail Azure Synapse et l’administrateur Azure Synapse Azure Active Directory (utilisateur ou membre de groupe) sont spécifiés dans le portail Azure :

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png" alt-text="Identification de l’administrateur de service par la consultation du champ Connexion d’administrateur SQL de l’espace de travail" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png":::

De même, les rôles de base de données propriétaire de la base de données (DBO) et **db_owner** ne sont pas autorisés à modifier leur classe de ressources par défaut. Si un utilisateur est le propriétaire de la base de données ou s’il a été ajouté sous le rôle de base de données **db_owner**, toutes les requêtes exécutées par l’utilisateur sont redirigées vers smallrc par défaut. Ces rôles ne peuvent pas être ajoutés à une classe de ressources autre que smallrc. Toutefois, si un utilisateur qui fait partie de ce rôle souhaite classer ses requêtes dans un autre groupe de charges de travail, il peut utiliser l’option MEMBERNAME dans la [définition du classifieur de la charge de travail](sql-data-warehouse-workload-classification.md).


### <a name="use-workload-group-precedence-for-better-classification"></a>Utiliser la priorité des groupes de charges de travail pour une meilleure classification

Dans les scénarios où les classifieurs de charge de travail sont mappés à plusieurs groupes de charges de travail, ou lorsqu’un utilisateur est mappé à plusieurs classes de ressources, l’ordre de priorité détermine le groupe de charges de travail et la classe de ressources choisis. Liste des règles de priorité : 

> [!NOTE]
> Comme mentionné dans la section [Mélanger des classes de ressources et la gestion des charges de travail définies par l’utilisateur](#mixed-usage-of-resource-classes-and-user-defined-workload-management), il est déconseillé de combiner l’utilisation des classes de ressources et des groupes de charges de travail/classes définies par l’utilisateur.

#### <a name="if-resource-classes-are-being-used"></a>En cas d’utilisation de classes de ressources :

Dans les scénarios où des classes de ressources sont utilisées, il est préférable de créer un utilisateur dédié pour chaque type de charge de travail en cours d’exécution. Toutefois, si un utilisateur est membre de plusieurs classes de ressources, les règles de priorité documentées dans [Précédence de classe de ressources](resource-classes-for-workload-management.md#resource-class-precedence) s’appliquent :

1. La classe de ressources dynamiques est prioritaire sur la classe de ressources statiques. Par exemple, si un utilisateur est membre de mediumrc (dynamique) et de staticrc80 (statique), les requêtes utilisateur s’exécutent avec mediumrc.
1. Des classes de ressources plus volumineuses sont préférables à des classes de ressources plus petites. Par exemple, si un utilisateur est membre de staticrc20 et staticrc80, les requêtes utilisateur s’exécutent avec staticrc80.

#### <a name="if-workload-management-capabilities-are-used"></a>En cas d’utilisation des fonctionnalités de gestion de la charge de travail

WLM permet de créer plusieurs classifieurs de charge de travail pour le même utilisateur ou groupe de charges de travail. L’instruction de définition de classifieur a plusieurs paramètres basés sur les requêtes entrantes qui sont affectés aux charges de travail. Ces paramètres ont un score de **poids** comme indiqué ci-dessous et ce score détermine l’ordre de priorité : 

|**Paramètre du classifieur** |**Poids**   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Découvrons ces règles de priorité en action à l’aide d’un exemple. Si un utilisateur crée deux classifieurs de charge de travail comme suit :

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,START_TIME     = '18:00' 
 ,END_TIME       = '07:30'
 ,IMPORTANCE     = 'Low')
```

Les requêtes soumises par l’Utilisateur-1 peuvent être soumises via les deux classifieurs. La requête exécutée par l’Utilisateur-1 avec l’étiquette « dimension_loads » entre 18 h 00 et 7 h UTC sera affectée à `wgDashboards`, car le score de poids de WLM_LABEL est supérieur à START_TIME/END_TIME. La pondération de `CLASSIFIER-1` est de 80 (64 pour l’utilisateur, plus 16 pour WLM_LABEL). La pondération de `CLASSIFIER-2` est de 68 (64 pour l’utilisateur, 4 pour START_TIME/END_TIME). 

Pour plus d’informations sur la classification des charges de travail, consultez [Pondération de la classification](sql-data-warehouse-workload-classification.md#classification-weighting) et [Étiquettes de requête](../sql/develop-label.md).

### <a name="what-happens-if-precedence-in-workload-classification-leads-to-a-tie"></a>Que se passe-t-il si la priorité dans la classification des charges de travail provoque un lien ?

Même après l’application de la priorité du classificateur de charge de travail, une requête peut être classée dans plusieurs groupes de charges de travail. Considérez par exemple les classifieurs suivants :

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'Low');
```

Si un utilisateur exécute une requête avec `OPTION (LABEL = 'dimension_loads')`, il peut être classé dans `wgDataLoad` ou `wgUserqueries` en fonction des règles de priorité. Mais quel groupe de charges de travail est choisi ?

#### <a name="workload-group-and-classifier-tie-breakers"></a>Décisions relatives aux groupes de charges de travail et aux classifieurs

En cas de lien entre les groupes de charges de travail et les classifieurs, la priorité suivante est appliquée :

1. Le groupe de charges de travail avec l’allocation de ressources la plus élevée est choisi. Ce comportement optimise les performances dans les scénarios où les connexions sont effectuées par des membres de plusieurs classes de ressources. Ce comportement garantit également une compatibilité descendante.  
    
Considérez les deux groupes de charges de travail et les classifieurs de charge de travail suivants :
    
```sql
-- Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 26 
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2              
,CAP_PERCENTAGE_RESOURCE = 100 )

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 15
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5               
,CAP_PERCENTAGE_RESOURCE = 50 );

--Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');
```
                  
Si un utilisateur exécute une requête avec `OPTION (LABEL = 'dimension_loads')`, la requête répond aux critères des deux classifieurs. Toutefois, la requête de l’utilisateur est acheminée vers le groupe de charges de travail `wgUserQueries`, car elle a une allocation de ressources minimale par requête.

2. Ensuite, le paramètre de concurrence et la concurrence disponible pour les groupes de charges de travail respectifs sont pris en compte pour les décisions. Le groupe de charges de travail avec la concurrence disponible la plus élevée à un moment où la requête est arrivée donne la meilleure chance d’exécution de la requête. 

Considérez les deux groupes de charges de travail et les classifieurs de charge de travail suivants :

```sql
--Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 30              
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 30
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

-- Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');
```
              
Si un utilisateur exécute une requête avec `OPTION (LABEL = 'dimension_loads')`, les deux classifieurs ont un lien, car la requête répond aux critères des deux. Lorsque l’utilisateur soumet la requête, envisagez le scénario dans lequel cinq requêtes simultanées sont exécutées dans le groupe `wgUserqueries` et dix requêtes sont exécutées dans le groupe `wgDataLoad`. La requête de l’utilisateur est acheminée vers le groupe `wgUserqueries` à l’aide de `CLASSIFIER-2`, car le groupe de charges de travail `wgUserqueries` a une concurrence disponible plus élevée au moment où l’utilisateur a envoyé une requête.

3. Ensuite, le paramètre d’importance de la requête est pris en compte pour les décisions. S’il existait un lien entre la classification de la charge de travail à l’aide des règles de priorité, la requête est acheminée vers le groupe de charges de travail dont l’importance est la plus élevée. Pour plus d’informations, consultez [Importance des charges de travail](sql-data-warehouse-workload-importance.md).

4. Enfin, l’heure de création du groupe de charges de travail est prise en compte pour les décisions. La requête de l’utilisateur est acheminée vers le groupe de charges de travail qui a été créé en dernier. 


## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la classification des charges de travail, consultez [Classification des charges de travail](sql-data-warehouse-workload-classification.md).
- Pour plus d’informations sur l’importance des charges de travail, consultez [Importance des charges de travail](sql-data-warehouse-workload-importance.md).

> [!div class="nextstepaction"]
> [Accédez à Configurer l’importance des charges de travail](sql-data-warehouse-how-to-configure-workload-importance.md)
 
