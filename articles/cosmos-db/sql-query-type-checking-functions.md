---
title: 'Langage de requête Azure Cosmos DB : fonctions de contrôle de type'
description: Découvrez les fonctions système SQL de contrôle de type dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 96fea7cb53c89c02583a6dba8434cd33641d6a09
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341499"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Fonctions de contrôle de type (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Les fonctions de vérification du type vous permettent de vérifier le type d’une expression donnée dans une requête SQL. Vous pouvez les utiliser pour déterminer instantanément le types variables ou inconnus des propriétés des éléments. 

## <a name="functions"></a>Fonctions

Le tableau liste les fonctions de vérification du type intégrées qui sont prises en charge :

Les fonctions suivantes prennent en charge la vérification de type par rapport aux valeurs d’entrée, et chacune renvoie une valeur booléenne.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)
- [Agrégats](sql-query-aggregates.md)
