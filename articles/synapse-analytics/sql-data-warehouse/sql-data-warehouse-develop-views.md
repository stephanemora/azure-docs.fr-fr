---
title: Utilisation des vues T-SQL
description: Conseils pour l’utilisation des vues T-SQL et le développement de solutions dans un pool SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633394"
---
# <a name="views-in-synapse-sql-pool"></a>Vues dans le pool SQL Synapse

Des vues peuvent être utilisées de différentes façons afin d’améliorer la qualité de votre solution.

Le pool SQL prend en charge les vues standard et matérialisées. Les deux sont des tables virtuelles créées avec des expressions SELECT et présentées aux requêtes sous forme de tables logiques.

Les vues encapsulent la complexité du calcul de données courant et ajoutent une couche d’abstraction aux modifications de calcul si bien qu’il n’est pas nécessaire de réécrire les requêtes.

## <a name="standard-view"></a>Vue standard

Une vue standard calcule ses données chaque fois que la vue est utilisée. Aucune donnée n’est stockée sur le disque. L’usage veut que les vues standard soient employés en tant qu’outil permettant d’organiser les objets logiques et les requêtes dans une base de données.

Pour utiliser une vue standard, une requête a besoin d’y faire référence directement. Pour plus d’informations, consultez la documentation [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Dans le pool SQL, les vues sont exclusivement stockées sous forme de métadonnées. De ce fait, les options suivantes ne sont pas disponibles :

* Il n’existe aucune option de liaison de schéma.
* Les tables de base ne peuvent pas être mises à jour par le biais de la vue.
* Vous ne pouvez pas créer des vues sur des tables temporaires.
* Les indications EXPAND/NOEXPAND ne sont pas prises en charge.
* Le pool SQL n’inclut aucune vue indexée.

Les vues standard peuvent être utilisées pour mettre en place des jointures plus performantes entre les tables. Ainsi, une vue peut inclure une clé de distribution redondante parmi ses critères de jointure, afin de réduire le nombre de déplacements de données.

Elle peut également permettre de forcer une indication de jointure ou de requête spécifique. Utiliser des vues de cette manière permet de garantir que les jointures sont toujours effectuées de façon optimale en évitant d’avoir à se souvenir de leur construction correcte.

## <a name="materialized-view"></a>Vue matérialisée

Une vue matérialisée précalcule, stocke et conserve ses données dans le pool SQL à l'instar d'une table. Aucun recalcul n’est nécessaire à chaque utilisation d’une vue matérialisée.

À mesure que les données sont chargées dans les tables de base, le pool SQL actualise de manière synchrone les vues matérialisées.  L’optimiseur de requête utilise automatiquement des vues matérialisées déployées pour améliorer les performances des requêtes, même si ces vues ne sont pas référencées dans la requête.  

Les requêtes qui tirent le meilleur parti des vues matérialisées sont des requêtes complexes (généralement des requêtes avec des jointures et des agrégations) sur des tables volumineuses qui produisent un jeu de résultats réduit.  

Pour plus d’informations sur la syntaxe d’une vue matérialisée et d’autres exigences, consultez [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Pour des conseils de paramétrage des requêtes, consultez [Réglage des performances avec des vues matérialisées](performance-tuning-materialized-views.md).

## <a name="example"></a>Exemple

Un modèle d’application courant consiste à recréer des tables au moyen de l’instruction CREATE TABLE AS SELECT (CTAS), suivi d’un modèle de changement de nom d’un objet lors du chargement des données.  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Toutefois, cette approche peut provoquer l’apparition et la disparition de tables d’une vue utilisateur, ainsi que l’affichage de messages d’erreur de type « la table n’existe pas ».

Vous pouvez utiliser des vues pour fournir aux utilisateurs une couche de présentation cohérente pendant que les objets sous-jacents sont renommés. L’accès aux données étant fourni via des vues, les utilisateurs n’ont pas besoin d’une visibilité sur les tables sous-jacentes.

Cette couche fournit une expérience utilisateur homogène, tout en garantissant que les concepteurs d’entrepôts de données puissent faire évoluer le modèle de données. Pouvoir faire évoluer les tables sous-jacentes signifie que les concepteurs peuvent utiliser CTAS pour optimiser les performances lors du processus de chargement de données.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires en matière de développement, consultez [Présentation du développement d’un pool SQL](sql-data-warehouse-overview-develop.md).
