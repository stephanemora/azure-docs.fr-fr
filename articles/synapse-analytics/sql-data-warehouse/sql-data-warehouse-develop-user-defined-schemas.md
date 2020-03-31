---
title: Utilisation de schémas définis par l’utilisateur
description: Conseils relatifs à l’utilisation de schémas T-SQL dans Azure SQL Data Warehouse pour le développement de solutions.
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
ms.openlocfilehash: a9ed4f01aae6ace1af6c1652fe3c5ecfe14dc6bf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351539"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Utilisation de schémas définis par l’utilisateur dans SQL Data Warehouse
Conseils relatifs à l’utilisation de schémas T-SQL dans Azure SQL Data Warehouse pour le développement de solutions.

## <a name="schemas-for-application-boundaries"></a>Schémas pour les limites d’application

Les entrepôts de données traditionnels utilisent souvent des bases de données distinctes pour créer des limites d’application basées sur la charge de travail, le domaine ou la sécurité. Par exemple, un entrepôt de données SQL Server classique peut inclure une base de données de la zone de transit, une base de données de l’entrepôt de données et quelques bases de données de mini-Data Warehouse. Dans cette topologie, chaque base de données joue le rôle d’étendue de sécurité et de charge de travail dans l’architecture.

À l’inverse, SQL Data Warehouse exécute l’intégralité de la charge de travail des entrepôts de données au sein d’une seule et même base de données. Les jointures entre plusieurs bases de données ne sont pas autorisées. Par conséquent, SQL Data Warehouse s’attend que l’ensemble des tables utilisées par l’entrepôt soient stockées au sein d’une seule et même base de données.

> [!NOTE]
> SQL Data Warehouse ne prend pas en charge les requêtes de base de données croisées, de quelque nature que ce soit. Par conséquent, les implémentations d’entrepôt de données tirant parti de ce modèle devront être modifiées.
> 
> 

## <a name="recommendations"></a>Recommandations
Tenez compte des recommandations lorsque vous consolidez des limites de charge de travail, fonctionnelles, de domaine et de sécurité :

1. Utilisez une base de données SQL Data Warehouse pour exécuter l’intégralité de la charge de travail des entrepôts de données.
2. Consolidez l’environnement d’entrepôt de données existant de manière à utiliser une seule base de données SQL Data Warehouse.
3. Tirez parti de **schémas définis par l’utilisateur** pour fournir la limite précédemment implémentée via des bases de données.

Si aucun schéma défini par l’utilisateur n’a été utilisé précédemment, vous repartez de zéro. Il vous suffit d’utiliser l’ancien nom de base de données en tant que base pour vos schémas définis par l’utilisateur dans la base de données SQL Data Warehouse.

Si les schémas ont été déjà utilisés, vous disposez de différentes options :

1. Vous pouvez supprimer les noms de schéma hérités et recommencer de zéro.
2. Vous pouvez conserver le nom de schéma hérité, et ajouter ce dernier au nom de la table.
3. Vous pouvez conserver le nom de schéma hérité, en implémentant les vues sur la table au sein d’un schéma supplémentaire pour recréer l’ancienne structure du schéma.

> [!NOTE]
> À première vue, la troisième option semble la plus tentante. Toutefois, si on l’étudie plus en détail, elle ne semble pas à la hauteur de ses promesses. Les vues sont en lecture seule dans SQL Data Warehouse. Toute modification portant sur les données ou la table doit être effectuée sur la table de base. Par ailleurs, cette troisième option présente une couche de vues au sein de votre système. Peut-être devriez-vous étudier la question plus attentivement si vous utilisez déjà des vues dans votre architecture.
> 
> 

### <a name="examples"></a>Exemples :
Implémentation de schémas définis par l’utilisateur basés sur des noms de base de données

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conservation du nom de schéma hérité et ajout au nom de la table ; utilisation de schémas pour la limite de charge de travail

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conservation des noms de schémas hérités via des vues

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Toute modification portant sur la stratégie de schéma nécessite une révision du modèle de sécurité de la base de données. Dans de nombreux cas, il est possible de simplifier le modèle de sécurité en attribuant des autorisations au niveau du schéma. S’il vous faut des autorisations plus granulaires, vous pouvez utiliser des rôles de base de données.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).

