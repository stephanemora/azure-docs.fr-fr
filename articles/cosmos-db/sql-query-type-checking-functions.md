---
title: 'Langage de requête Azure Cosmos DB : fonctions de contrôle de type'
description: Découvrez les fonctions système SQL de contrôle de type dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7555c1a1789cc33a75f78f9f3bf8ff4e5a3e3af2
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539220"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Fonctions de contrôle de type (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Les fonctions de vérification du type vous permettent de vérifier le type d’une expression donnée dans une requête SQL. Vous pouvez les utiliser pour déterminer instantanément le types variables ou inconnus des propriétés des éléments. 

## <a name="functions"></a>Fonctions

Les fonctions suivantes prennent en charge la vérification de type par rapport aux valeurs d’entrée, et chacune renvoie une valeur booléenne. La colonne **Utilisation de l’index** suppose, le cas échéant, que vous comparez les fonctions de vérification de type à une autre valeur avec un filtre d’égalité.

| Fonction système                           | Utilisation de l’index | [Utilisation de l’index dans les requêtes avec les fonctions d’agrégation scalaires](index-overview.md#index-utilization-for-scalar-aggregate-functions) | Notes |
| ----------------------------------------- | ----------- | ------------------------------------------------------------ | ------- |
| [IS_ARRAY](sql-query-is-array.md)         | Analyse complète   | Analyse complète                                                    |         |
| [IS_BOOL](sql-query-is-bool.md)           | Recherche dans l'index  | Recherche dans l'index                                                   |         |
| [IS_DEFINED](sql-query-is-defined.md)     | Recherche dans l'index  | Recherche dans l'index                                                   |         |
| [IS_NULL](sql-query-is-null.md)           | Recherche dans l'index  | Recherche dans l'index                                                   |         |
| [IS_NUMBER](sql-query-is-number.md)       | Recherche dans l'index  | Recherche dans l'index                                                   |         |
| [IS_OBJECT](sql-query-is-object.md)       | Analyse complète   | Analyse complète                                                    |         |
| [IS_PRIMITIVE](sql-query-is-primitive.md) | Recherche dans l'index  | Recherche dans l'index                                                   |         |
| [IS_STRING](sql-query-is-string.md)       | Recherche dans l'index  | Recherche dans l'index                                                   |         

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)
- [Agrégats](sql-query-aggregate-functions.md)
