---
title: 'Langage de requête Azure Cosmos DB : StringToBoolean'
description: Découvrez la fonction système SQL StringToBoolean dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d221fb9a738d1b508b08e605ffa3c5d1c927317a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563529"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne une expression convertie en booléen. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne à analyser comme expression booléenne.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne ou un résultat indéfini.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment `StringToBoolean` se comporte avec différents types. 
 
 Voici quelques exemples avec une entrée valide.

L’espace blanc est autorisé uniquement avant ou après « true »/« false ».

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Voici quelques exemples avec une entrée non valide.

 Les valeurs booléennes respectent la casse et doivent être écrites avec tous les caractères en minuscules, c’est-à-dire, « true » et « false ».

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Voici le jeu de résultats obtenu.  
  
```json
[{}]
``` 

L’expression passée est analysée en tant qu’expression booléenne ; ces entrées n’étant pas évaluées comme étant de type booléen, une valeur non définie est retournée.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Voici le jeu de résultats obtenu.  
  
```json
[{}]
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
