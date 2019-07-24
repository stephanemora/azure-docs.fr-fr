---
title: Affecter des variables dans Azure SQL Data Warehouse | Microsoft Docs
description: Conseils relatifs à l’affectation de variables T-SQL dans Azure SQL Data Warehouse, dans le cadre du développement de solutions.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 62c4273a02e02aff268a96e1b13483088ba33f87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861674"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Affectation de variables dans Azure SQL Data Warehouse

Conseils relatifs à l’affectation de variables T-SQL dans Azure SQL Data Warehouse, dans le cadre du développement de solutions.

## <a name="setting-variables-with-declare"></a>Définition de variables via l’instruction DECLARE

Dans SQL Data Warehouse, les variables sont définies au moyen de l’instruction `DECLARE` ou `SET`. Dans SQL Data Warehouse, l’initialisation de variables avec l’instruction DECLARE constitue l’une des méthodes les plus flexibles pour définir une valeur de variable.

```sql
DECLARE @v  int = 0
;
```

De plus, vous pouvez utiliser cette instruction pour définir plusieurs variables à la fois. Vous ne pouvez pas utiliser SELECT ou UPDATE pour effectuer les opérations suivantes :

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Il n’est pas possible d’initialiser et d’utiliser une variable au sein de la même instruction DECLARE. Illustrons notre propos : la commande de l’exemple suivant n’est **pas** autorisée, car l’élément @p1 est initialisé, mais également utilisé dans la même instruction DECLARE. L’exemple suivant génère une erreur.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Définition de valeurs avec l’instruction SET

L’instruction SET est couramment utilisée pour définir une variable unique.

Les instructions suivantes sont toutes valides pour définir une variable avec l’instruction SET :

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Avec l’instruction SET, vous pouvez uniquement définir une variable à la fois. Toutefois, les opérateurs composés sont autorisés.

## <a name="limitations"></a>Limites

Vous ne pouvez pas utiliser l’instruction UPDATE pour attribuer des variables.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).
