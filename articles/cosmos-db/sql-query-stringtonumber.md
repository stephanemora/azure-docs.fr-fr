---
title: 'Langage de requête Azure Cosmos DB : StringToNumber'
description: Découvrez la fonction système SQL StringToNumber dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b9596738d9b02fa26f9c363287323b905654a1f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349229"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Retourne une expression convertie en nombre. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne à analyser comme expression de nombre JSON. En JSON, un nombre doit être un entier ou un nombre à virgule flottante. Pour plus d’informations sur le format JSON, consultez [json.org](https://json.org/)  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de nombre ou un résultat indéfini.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment `StringToNumber` se comporte avec différents types. 

L’espace blanc est autorisé uniquement avant ou après le nombre.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

En JSON, un nombre valide doit être un entier ou un nombre à virgule flottante.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
{{}}
```  

L’expression passée est analysée en tant qu’expression de nombre ; ces entrées n’étant pas évaluées comme étant de type nombre, une valeur non définie est retournée. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
{{}}
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
