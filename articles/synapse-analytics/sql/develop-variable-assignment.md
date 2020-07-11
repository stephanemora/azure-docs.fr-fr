---
title: Attribuer des variables avec SQL Synapse
description: Dans cet article, vous trouverez des conseils pour l’attribution de variables T-SQL avec SQL Synapse.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 91e251c8274ad85fcb0b3a65a3bdbcc660ceba00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85101008"
---
# <a name="assigning-variables-with-synapse-sql"></a>Attribution de variables avec SQL Synapse

Dans cet article, vous trouverez des conseils pour l’attribution de variables T-SQL avec SQL Synapse.

## <a name="setting-variables-with-declare"></a>Définition de variables via l’instruction DECLARE

Dans SQL Synapse, les variables sont définies au moyen de l’instruction `DECLARE` ou `SET`. L’initialisation de variables avec l’instruction DECLARE constitue l’une des méthodes les plus flexibles pour définir une valeur de variable dans SQL Synapse.

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

Il n’est pas possible d’initialiser et d’utiliser une variable au sein de la même instruction DECLARE. Illustrons notre propos : l’exemple suivant n’est pas autorisé car l’élément *\@p1* est à la fois initialisé et utilisé dans la même instruction DECLARE. L’exemple suivant génère une erreur.

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

Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Présentation du développement SQL Synapse](develop-overview.md).
