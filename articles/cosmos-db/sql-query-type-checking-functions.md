---
title: 'Langage de requête Azure Cosmos DB : fonctions de contrôle de type'
description: Découvrez les fonctions système SQL de contrôle de type dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3ffe8fa9286c8594dc72d78582f8c7b0a3d05c78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85563343"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Fonctions de contrôle de type (Azure Cosmos DB)

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
