---
title: Commandes Hive LLAP Workload Management
titleSuffix: Azure HDInsight
description: Commandes Hive LLAP Workload Management
ms.service: hdinsight
ms.topic: reference
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: dc05ae5ec7cad35d5cda549e654caf3d44d91a03
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482182"
---
# <a name="hive-llap-workload-management-commands"></a>Commandes Hive LLAP Workload Management

La fonctionnalité de gestion des charges de travail peut être contrôlée et managée à l’aide des commandes Hive suivantes. Ces commandes ressemblent aux instructions ALTER, CREATE, DROP et SHOW existantes.

## <a name="alter-mapping"></a>Modifier le mappage 
Modifie le routage des requêtes vers un pool de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
ALTER { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ]
```
#### <a name="example"></a>Exemple 
```hql
ALTER USER MAPPING 'hive' IN demo_plan TO etl WITH ORDER 1;
```

## <a name="alter-pool"></a>Modifier le pool 
Modifie les propriétés du pool de requêtes, ajoute et supprime des déclencheurs. 
#### <a name="syntax"></a>Syntaxe 
```hql
ALTER POOL plan_name.pool_path [ SET {property=value, ... } | UNSET { property, ... } ];
ALTER POOL plan_name.pool_path [ ADD | DROP ] TRIGGER name;
```
#### <a name="example"></a>Exemple 
```hql
ALTER POOL demo_plan.default ADD TRIGGER defaultToETL;
```

## <a name="alter-resource-plan"></a>Modifier le plan de ressources 
Active, désactive, actionne, valide ou modifie un plan. 
#### <a name="syntax"></a>Syntaxe 
```hql
ALTER RESOURCE PLAN name [ VALIDATE | DISABLE | ENABLE | ACTIVATE | RENAME TO another_name | SET {property=value, ... } | UNSET {property, ... } ];
```
#### <a name="example"></a>Exemple 
```hql
ALTER RESOURCE PLAN demo_plan SET DEFAULT POOL=etl, QUERY_PARALLELISM=3;
```

## <a name="alter-trigger"></a>Modifier le déclencheur 
Ajoute ou supprime un déclencheur dans un pool de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
ALTER TRIGGER plan_name.name { ADD TO | DROP FROM } { POOL path | UNMANAGED };
```
#### <a name="example"></a>Exemple 
```hql
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;
```

## <a name="create-mapping"></a>Créer un mappage 
Achemine les requêtes vers un pool de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
CREATE { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ];
```
#### <a name="example"></a>Exemple 
```hql
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
```

## <a name="create-pool"></a>Créer un pool 
Crée et ajoute un pool de requêtes pour un plan de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
CREATE POOL plan_name.path WITH ALLOC_FRACTION = decimal, QUERY_PARALLELISM = num, [ SCHEDULING_POLICY = scheduling_value ];
```
#### <a name="example"></a>Exemple 
```hql
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
```

## <a name="create-resource-plan"></a>Créer un plan de ressources 
Crée un plan de ressources 
#### <a name="syntax"></a>Syntaxe 
```hql
CREATE RESOURCE PLAN plan_name [ WITH QUERY PARALLELISM=number | LIKE name];
```
#### <a name="example"></a>Exemple 
```hql
CREATE RESOURCE PLAN demo_plan;
```

## <a name="create-trigger"></a>Créer un déclencheur 
Crée et ajoute un déclencheur pour un plan de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
CREATE TRIGGER plan_name.name WHEN condition DO action;
```
#### <a name="example"></a>Exemple 
```hql
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
```

## <a name="disable-workload-management"></a>Désactiver la gestion des charges de travail 
Désactive le plan de ressources actif. 
#### <a name="syntax"></a>Syntaxe 
```hql
DISABLE WORKLOAD MANAGEMENT;
```
#### <a name="example"></a>Exemple 
```hql
DISABLE WORKLOAD MANAGEMENT
```

## <a name="drop-mapping"></a>Supprimer un mappage 
Supprime un mappage dans un plan de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
DROP { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name;
```
#### <a name="example"></a>Exemple 
```hql
DROP USER MAPPING 'hive' IN demo_plan;
```

## <a name="drop-pool"></a>Supprimer un pool 
Supprime un pool de requêtes dans un plan de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
DROP POOL plan_name.pool_path;
```
#### <a name="example"></a>Exemple 
```hql
CREATE POOL demo_plan.etl;
```

## <a name="drop-resource-plan"></a>Supprimer un plan de ressources 
Supprime un plan de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
DROP RESOURCE PLAN plan_name;
```
#### <a name="example"></a>Exemple 
```hql
DROP RESOURCE PLAN demo_plan;
```

## <a name="drop-trigger"></a>Supprimer un déclencheur 
Supprime un déclencheur dans un plan de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
DROP TRIGGER plan_name.trigger_name;
```
#### <a name="example"></a>Exemple 
```hql
DROP TRIGGER demo_plan.defaultToETL;
```

## <a name="replace-resource-plan-with"></a>Remplacer le plan de ressources par 
Remplace le contenu d’un plan de ressources par le contenu d’un autre. 
#### <a name="syntax"></a>Syntaxe 
```hql
REPLACE RESOURCE PLAN name1 WITH name2; 
REPLACE ACTIVE RESOURCE PLAN name1 WITH name2;
```
#### <a name="example"></a>Exemple 
```hql
REPLACE RESOURCE PLAN rp_plan1 WITH rp_plan2;
```

## <a name="show-resource-plan"></a>Afficher un plan de ressources 
Répertorie le contenu du plan. 
#### <a name="syntax"></a>Syntaxe 
```hql
SHOW RESOURCE PLAN plan_name;
```
#### <a name="example"></a>Exemple 
```hql
SHOW RESOURCE PLAN demo_plan;
```

## <a name="show-resource-plans"></a>Afficher les plans de ressources 
Répertorie tous les plans de ressources. 
#### <a name="syntax"></a>Syntaxe 
```hql
SHOW RESOURCE PLANS;
```
#### <a name="example"></a>Exemple 
```hql
SHOW RESOURCE PLANS;
```
