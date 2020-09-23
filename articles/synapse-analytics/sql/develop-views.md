---
title: Vues T-SQL avec SQL Synapse
description: Conseils pour l’utilisation de vues T-SQL et le développement de solutions avec SQL Synapse.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fafa0c2e1b02cc49bfb852ed7770b0927b0e9334
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032722"
---
# <a name="t-sql-views-using-synapse-sql"></a>Vues T-SQL avec SQL Synapse
Cet article fournit des conseils pour l’utilisation de vues T-SQL et le développement de solutions avec SQL Synapse. 

## <a name="why-use-views"></a>Pourquoi utiliser des vues

Des vues peuvent être utilisées de différentes façons afin d’améliorer la qualité de votre solution.  Cet article met en évidence présente exemples montrant comment enrichir votre solution avec des vues, ainsi que les limites à prendre en considération.

### <a name="sql-pool---create-view"></a>Pool SQL – Créer une vue

> [!NOTE]
> **Pool SQL** : La syntaxe de CREATE VIEW n’est pas abordée dans cet article. Pour plus d’informations, consultez la documentation [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="sql-on-demand-preview---create-view"></a>SQL à la demande (préversion) – Créer une vue

> [!NOTE]
> **SQL à la demande** : La syntaxe de CREATE VIEW n’est pas abordée dans cet article. Pour plus d’informations, consultez la documentation [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Abstraction architecturale

Un modèle d’application courant consiste à opérer une recréation de tables à l’aide de la fonction [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), suivie de l’application d’un modèle de changement de nom d’objet lors du chargement des données.

L’exemple suivant permet d’ajouter de nouveaux enregistrements de date à une dimension de date. Notez comment une nouvelle table, DimDate_New, est d’abord créée, puis renommée pour remplacer la version d’origine de la table.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Gardez à l’esprit que cette approche peut avoir pour effet que des tables apparaissent et disparaissent pour l’utilisateur, et que des messages d’erreur « Cette table n’existe pas » s’affichent. Vous pouvez utiliser des vues pour fournir aux utilisateurs une couche de présentation cohérente pendant que les objets sous-jacents sont renommés.

L’accès aux données étant fourni via des vues, les utilisateurs n’ont pas besoin de voir les tables sous-jacentes. En plus d’une expérience utilisateur cohérente, cette couche garantit que les concepteurs d’analyse peuvent faire évoluer le modèle de données. La possibilité de faire évoluer les tables sous-jacentes signifie que les concepteurs peuvent utiliser CTAS pour optimiser les performances lors du processus de chargement de données.

## <a name="performance-optimization"></a>Optimisation des performances

Il est également possible d’utiliser des vues pour mettre en place entre les tables des jointures aux performances optimisées. Ainsi, une vue peut inclure une clé de distribution redondante parmi ses critères de jointure, afin de réduire le nombre de déplacements de données.

L’exécution forcée d’une requête spécifique ou l’adjonction d’un conseil est un autre avantage lié à l’utilisation de vues T-SQL. Par conséquent, la fonctionnalité des vues garantit que les jointures sont toujours effectuées de manière optimale. Cela évite aux utilisateurs de devoir se souvenir de la construction correcte pour leurs jointures.

## <a name="limitations"></a>Limites

Les vues dans SQL Synapse sont stockées uniquement en tant que métadonnées. De ce fait, les options suivantes ne sont pas disponibles :

* Il n’existe aucune option de liaison de schéma.
* Il n’est pas possible de mettre à jour les tables de base via la vue.
* Il n’est pas possible de créer des vues sur des tables temporaires.
* Les conseils EXPAND/NOEXPAND ne sont pas pris en charge.
* Il n’existe pas de vue indexée dans SQL Synapse.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires en matière de développement, consultez [Présentation du développement SQL Synapse](develop-overview.md).



