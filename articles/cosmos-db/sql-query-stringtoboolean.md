---
title: 'Langage de requête Azure Cosmos DB : StringToBoolean'
description: Découvrez la fonction système SQL StringToBoolean dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349259"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
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

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
