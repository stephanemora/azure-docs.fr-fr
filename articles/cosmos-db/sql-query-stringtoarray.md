---
title: 'Langage de requête Azure Cosmos DB : StringToArray'
description: Découvrez la fonction système SQL StringToArray dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302914"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Retourne une expression convertie en tableau. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
## <a name="syntax"></a>Syntaxe
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne à analyser comme expression de tableau JSON. 
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de tableau ou une valeur non définie. 
  
## <a name="remarks"></a>Notes
  Les valeurs de chaîne imbriquées doivent être écrites avec des guillemets doubles pour être valides dans le code JSON. Pour plus d’informations sur le format JSON, consultez [json.org](https://json.org/)
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment `StringToArray` se comporte avec différents types. 
  
 Voici quelques exemples avec une entrée valide.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Voici le jeu de résultats obtenu.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Voici un exemple d’entrée non valide. 
   
 Les guillemets simples dans le tableau ne sont pas valides en JSON.
Même s’ils sont valides dans une requête, leur analyse ne permet pas d’obtenir un tableau valide. Les chaînes dans la chaîne de tableau doivent être placées dans une séquence d’échappement ("[\\"\\"]") ou les guillemets de début et de fin doivent être des guillemets simples ('[""]').

```sql
SELECT
    StringToArray("['5','6','7']")
```

Voici le jeu de résultats obtenu.

```json
[{}]
```

Voici quelques exemples d’entrée non valide.
   
 L’expression passée est analysée en tant que tableau JSON ; ce qui suit n’étant pas évalué comme étant de type tableau, une valeur non définie est retournée.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
- [Présentation d’Azure Cosmos DB](introduction.md)
