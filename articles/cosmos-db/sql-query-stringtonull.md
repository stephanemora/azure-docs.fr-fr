---
title: 'Langage de requête Azure Cosmos DB : StringToNull'
description: Découvrez la fonction système SQL StringToNull dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349244"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Retourne une expression convertie en valeur Null. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne à analyser comme expression Null.
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression Null ou un résultat indéfini.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment `StringToNull` se comporte avec différents types. 

Voici quelques exemples avec une entrée valide.

 L’espace blanc est autorisé uniquement avant ou après « null ».

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Voici quelques exemples avec une entrée non valide.

La valeur Null respecte la casse et doit être écrite avec tous les caractères en minuscules, c’est-à-dire, « null ».

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{}]
```  

L’expression passée est analysée en tant qu’expression Null ; ces entrées n’étant pas évaluées comme étant de type Null, une valeur non définie est retournée.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
