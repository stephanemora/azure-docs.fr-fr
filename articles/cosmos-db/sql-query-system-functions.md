---
title: Fonctions système
description: Découvrez les fonctions système SQL dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b0e9c751d46f805af75196da464a39783c95ae6a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619983"
---
# <a name="system-functions"></a>Fonctions système

 Cosmos DB fournit de nombreuses fonctions SQL intégrées. Les catégories de fonctions intégrées sont répertoriées ci-dessous.  
  
|Fonction|Description|  
|--------------|-----------------|  
|[Fonctions mathématiques](#mathematical-functions)|Chaque fonction mathématique effectue un calcul, généralement basé sur les valeurs d'entrée fournies comme arguments, et retourne une valeur numérique.|  
|[Fonctions de vérification du type](#type-checking-functions)|Les fonctions de vérification du type vous permettent de vérifier le type d'une expression donnée dans les requêtes SQL.|  
|[Fonctions de chaîne](#string-functions)|Les fonctions de chaîne effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur de type chaîne, une valeur numérique ou une valeur booléenne.|  
|[Fonctions de tableau](#array-functions)|Les fonctions de tableau effectuent une opération sur une valeur d’entrée de tableau et retournent une valeur numérique, une valeur booléenne ou une valeur de tableau.|
|[Fonctions de date et heure](#date-time-functions)|Les fonctions de date et heure vous permettent d’obtenir les date et heure UTC actuelles sous deux formes : un horodatage numérique dont la valeur est l’époque Unix, en millisecondes, ou une chaîne conforme au format ISO 8601.|
|[Fonctions spatiales](#spatial-functions)|Les fonctions spatiales effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur d’entrée d’objet spatial, une valeur numérique ou une valeur booléenne.|  

Voici une liste de fonctions au sein de chaque catégorie :

| Groupe de fonctions | Opérations |
|---------|----------|
| Fonctions mathématiques | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, RAND, SIN, TAN |
| Fonctions de vérification de type | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Fonctions de chaîne | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Fonctions de tableau | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH et ARRAY_SLICE |
| Fonctions de date et heure | GETCURRENTDATETIME, GETCURRENTTIMESTAMP  |
| Fonctions spatiales | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Si vous utilisez actuellement une fonction définie par l’utilisateur pour laquelle une fonction intégrée est désormais disponible, la fonction intégrée correspondante s’exécute plus rapidement et est plus performante.

La principale différence entre les fonctions Cosmos DB et les fonctions SQL ANSI est que les fonctions Cosmos DB sont conçues pour s’exécuter avec des données sans schéma et des données avec un schéma mixte. Par exemple, si une propriété est manquante ou a une valeur non numérique, telle que `unknown`, l’élément est ignoré et aucune erreur n’est retournée.

##  <a name="mathematical-functions"></a> Fonctions mathématiques  

Chaque fonction mathématique effectue un calcul, basé sur les valeurs d’entrée fournies comme arguments, et renvoie une valeur numérique.

Vous pouvez exécuter des requêtes similaires à l’exemple suivant :

```sql
    SELECT VALUE ABS(-4)
```

Le résultat est le suivant :

```json
    [4]
```

Ce tableau répertorie les fonctions mathématiques intégrées qui sont prises en charge.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[RAND](#bk_rand)|
|[SIN](#bk_sin)|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|
|[SIGN](#bk_sign)|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Retourne la valeur (positive) absolue de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant montre les résultats de l’utilisation de la fonction ABS sur trois nombres différents.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelée arccosinus.  
  
 **Syntaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant retourne la valeur ACOS de -1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Retourne l’angle, en radians, dont le sinus est l’expression numérique spécifiée. Cette fonction est également appelée arcsinus.  
  
 **Syntaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant retourne la valeur ASIN de -1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Cette fonction est également appelée arctangente.  
  
 **Syntaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant renvoie l’ATAN de la valeur spécifiée.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Retourne la valeur principale de l’arc tangente de y/x, exprimée en radians.  
  
 **Syntaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant calcule l’ATN2 pour les composants x et y spécifiés.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Retourne le plus petit nombre entier qui est supérieur ou égal à l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant montre des valeurs numériques positives, négatives et nulles avec la fonction CEILING.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
 **Syntaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant calcule le COS de l’angle spécifié.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant calcule la COT (cotangente) de l’angle spécifié.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Retourne l’angle correspondant en degrés d’un angle spécifié en radians.  
  
 **Syntaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant retourne le nombre de degrés d’un angle de PI/2 radians.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant montre des valeurs numériques positives, négatives et nulles avec la fonction FLOOR.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Retourne la valeur exponentielle de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Remarques**  
  
  La constante **e** (2,718281...), est la base des logarithmes naturels.  
  
  L’exposant d’un nombre correspond à la constante **e** élevée à la puissance du nombre. Par exemple, EXP(1.0) = e^1.0 = 2.71828182845905 et EXP(10) = e^10 = 22026.4657948067.  
  
  La valeur exponentielle du logarithme naturel d’un nombre est le nombre lui-même : EXP (LOG (n)) = n. Et le logarithme naturel de la valeur exponentielle d’un nombre est le nombre lui-même : LOG (EXP (n)) = n.  
  
  **Exemples**  
  
  L’exemple suivant déclare une variable et renvoie la valeur exponentielle de la variable spécifiée (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 L’exemple suivant retourne la valeur exponentielle du logarithme naturel de 20 et le logarithme naturel de la valeur exponentielle de 20. Étant donné que ces fonctions sont l’inverse l’une de l’autre, la valeur renvoyée avec arrondi pour les opérations mathématiques à virgule flottante dans les deux cas est 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Retourne le logarithme naturel de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
- `base`  
  
   L’argument numérique facultatif qui définit la base du logarithme.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Notes**  
  
  Par défaut, LOG() renvoie le logarithme naturel. Vous pouvez modifier la base du logarithme avec une autre valeur en utilisant le paramètre de base facultatif.  
  
  Le logarithme naturel est le logarithme de base **e**, où **e** est une constante irrationnelle approximativement égale à 2,718281828.  
  
  Le logarithme naturel de la valeur exponentielle d’un nombre est le nombre lui-même : LOG( EXP( n ) ) = n. Et la valeur exponentielle du logarithme naturel d’un nombre est le nombre lui-même : EXP( LOG( n ) ) = n.  
  
  **Exemples**  
  
  L’exemple suivant déclare une variable et renvoie la valeur du logarithme de la variable spécifiée (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 L’exemple suivant calcule le LOG pour l’exposant d’un nombre.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Retourne le logarithme en base 10 de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Remarques**  
  
  Les fonctions LOG10 et POWER sont inversement liées entre elles. Par exemple, 10 ^ LOG10 (n) = n.  
  
  **Exemples**  
  
  L’exemple suivant déclare une variable et renvoie la valeur de LOG10 de la variable spécifiée (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Retourne la valeur constante de PI.  
  
 **Syntaxe**  
  
```  
PI ()  
```  
   
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant retourne la valeur de PI.  
  
```  
SELECT PI() AS pi 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Retourne la valeur de l’expression spécifiée élevée à la puissance spécifiée.  
  
 **Syntaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
- `y`  
  
   La puissance à laquelle élever `numeric_expression`.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant montre l’élévation d’un nombre à la puissance 3 (le cube du nombre).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Retourne des radians lorsqu’une expression numérique, en degrés, est entrée.  
  
 **Syntaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant prend plusieurs angles comme entrée et retourne les valeurs correspondantes en radians.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Voici le jeu de résultats obtenu.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Retourne une valeur numérique, arrondie au nombre entier le plus proche.  
  
 **Syntaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Remarques**
  
  L’opération arrondit les valeurs médianes à la valeur la plus éloignée de zéro. Si l’entrée est une expression numérique qui se situe exactement entre deux entiers, le résultat est le nombre entier le plus éloigné de zéro.  
  
  |<numeric_expression>|Arrondi|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0.5|1|
  |6,5000|7||
  
  **Exemples**  
  
  L’exemple suivant arrondit les nombres positifs et négatifs suivants à l’entier le plus proche.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Voici le jeu de résultats obtenu.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

#### <a name="bk_rand"></a> RAND
 Retourne une valeur numérique générée de façon aléatoire à partir de [0, 1).
 
 **Syntaxe**  
  
```  
RAND ()  
```  

  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant retourne une valeur numérique générée de façon aléatoire.  
  
```  
SELECT RAND() AS rand 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"rand": 0.87860053195618093}]  
``` 

####  <a name="bk_sign"></a> SIGN  
 Retourne le signe positif (+1), nul (0) ou négatif (-1) de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant renvoie les valeurs SIGN des nombres de -2 à 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Retourne le sinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
 **Syntaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant calcule le SIN de l’angle spécifié.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Retourne la racine carrée de la valeur numérique spécifiée.  
  
 **Syntaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant renvoie les racines carrées des nombres de 1 à 3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Retourne le carré de la valeur numérique spécifiée.  
  
 **Syntaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant renvoie les carrés des nombres de 1 à 3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Retourne la tangente de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
 **Syntaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant calcule la tangente de PI()/ 2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Retourne une valeur numérique, tronquée au nombre entier le plus proche.  
  
 **Syntaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Arguments**  
  
- `numeric_expression`  
  
   Est une expression numérique.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant tronque les nombres positifs et négatifs suivants à la valeur entière la plus proche.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>Fonctions de vérification du type

Les fonctions de vérification du type vous permettent de vérifier le type d’une expression donnée dans une requête SQL. Vous pouvez les utiliser pour déterminer instantanément le types variables ou inconnus des propriétés des éléments. Le tableau liste les fonctions de vérification du type intégrées qui sont prises en charge :

Les fonctions suivantes prennent en charge la vérification de type par rapport aux valeurs d’entrée, et chacune renvoie une valeur booléenne.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type tableau.  
  
 **Syntaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type booléen.  
  
 **Syntaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété.  
  
 **Syntaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie la présence d’une propriété dans le document JSON spécifié. La première fonction retourne la valeur true, car « a » est présent, mais la seconde retourne false, car « b » est absent.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est de type null.  
  
 **Syntaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type nombre.  
  
 **Syntaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type objet JSON.  
  
 **Syntaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est de type primitif (chaîne, booléen, numérique ou null).  
  
 **Syntaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type chaîne.  
  
 **Syntaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Arguments**  
  
- `expression`  
  
   Peut être toute expression valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Fonction de chaîne

Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur de type chaîne, une valeur numérique ou une valeur booléenne :
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Retourne une chaîne qui est le résultat de la concaténation d’au moins deux valeurs de chaîne.  
  
 **Syntaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant renvoie la chaîne concaténée des valeurs spécifiées.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde.  
  
 **Syntaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie si « abc » contient « ab » et « d ».  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.  
  
 **Syntaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant indique si « abc » se termine par « b » et « bc ».  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Retourne la position de départ de la première occurrence de la seconde expression de chaîne dans la première expression de chaîne spécifiée, ou -1 si la chaîne est introuvable.  
  
 **Syntaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant retourne l’index de diverses sous-chaînes à l’intérieur de « abc ».  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Retourne la partie gauche d’une chaîne avec le nombre de caractères spécifié.  
  
 **Syntaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
- `num_expr`  
  
   Est une expression numérique valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant retourne la partie gauche de « abc » pour différentes valeurs de longueur.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 Retourne le nombre de caractères de l’expression de chaîne spécifiée.  
  
 **Syntaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant retourne la longueur d’une chaîne.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Retourne une expression de chaîne après la conversion des caractères majuscules en caractères minuscules.  
  
 **Syntaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant explique comment utiliser LOWER dans une requête.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Retourne une expression de chaîne après avoir supprimé les espaces de début.  
  
 **Syntaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment utiliser LTRIM dans une requête.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 Remplace toutes les occurrences d’une valeur de chaîne spécifiée par une autre valeur de chaîne.  
  
 **Syntaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment utiliser REPLACE dans une requête.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Répète une valeur de chaîne un nombre de fois spécifié.  
  
 **Syntaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
- `num_expr`  
  
   Est une expression numérique valide. Si num_expr est négative ou non finie, le résultat est indéfini.

  > [!NOTE]
  > La longueur maximale du résultat est 10 000 caractères ; par exemple, (length(str_expr)  *  num_expr) < = 10 000.
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment utiliser REPLICATE dans une requête.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Retourne l’ordre inverse d’une valeur de chaîne.  
  
 **Syntaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment utiliser REVERSE dans une requête.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Retourne la partie droite d’une chaîne avec le nombre de caractères spécifié.  
  
 **Syntaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
- `num_expr`  
  
   Est une expression numérique valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant retourne la partie droite de « abc » pour différentes valeurs de longueur.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Retourne une expression de chaîne après avoir supprimé les espaces de fin.  
  
 **Syntaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment utiliser RTRIM dans une requête.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde.  
  
 **Syntaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant vérifie si la chaîne « abc » commence par « b » et « a ».  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Retourne une expression convertie en tableau. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
 **Syntaxe**  
  
```  
StringToArray(<expr>)  
```  
  
 **Arguments**  
  
- `expr`  
  
   Toute expression scalaire valide à évaluer en tant qu’expression de tableau JSON. Notez que les valeurs de chaîne imbriquées doivent être écrites avec des guillemets doubles pour être valides. Pour plus d’informations sur le format JSON, consultez [json.org](https://json.org/)
  
  **Types de retour**  
  
  Retourne une expression de tableau ou une valeur non définie.  
  
  **Exemples**  
  
  L’exemple suivant montre comment StringToArray se comporte avec différents types. 
  
 Voici quelques exemples avec une entrée valide.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Voici le jeu de résultats obtenu.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Voici un exemple d’entrée non valide. 
   
 Les guillemets simples dans le tableau ne sont pas valides en JSON.
Même s’ils sont valides dans une requête, leur analyse ne permet pas d’obtenir un tableau valide. Les chaînes dans la chaîne de tableau doivent être placées dans une séquence d’échappement ("[\\"\\"]") ou les guillemets de début et de fin doivent être des guillemets simples ('[""]').

```
SELECT
    StringToArray("['5','6','7']")
```

Voici le jeu de résultats obtenu.

```
[{}]
```

Voici quelques exemples d’entrée non valide.
   
 L’expression passée est analysée en tant que tableau JSON ; ce qui suit n’étant pas évalué comme étant de type tableau, une valeur non définie est retournée.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Voici le jeu de résultats obtenu.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Retourne une expression convertie en booléen. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
 **Syntaxe**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Arguments**  
  
- `expr`  
  
   Toute expression scalaire valide à évaluer en tant qu’expression booléenne.  
  
  **Types de retour**  
  
  Retourne une expression booléenne ou un résultat indéfini.  
  
  **Exemples**  
  
  L’exemple suivant montre comment StringToBoolean se comporte avec différents types. 
 
 Voici quelques exemples avec une entrée valide.

L’espace blanc est autorisé uniquement avant ou après « true »/« false ».

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Voici quelques exemples avec une entrée non valide.

 Les valeurs booléennes respectent la casse et doivent être écrites avec tous les caractères en minuscules, c’est-à-dire, « true » et « false ».

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Voici le jeu de résultats obtenu.  
  
```  
[{}]
``` 

L’expression passée est analysée en tant qu’expression booléenne ; ces entrées n’étant pas évaluées comme étant de type booléen, une valeur non définie est retournée.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Voici le jeu de résultats obtenu.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Retourne une expression convertie en valeur Null. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
 **Syntaxe**  
  
```  
StringToNull(<expr>)  
```  
  
 **Arguments**  
  
- `expr`  
  
   Toute expression scalaire valide à évaluer en tant qu’expression Null.
  
  **Types de retour**  
  
  Retourne une expression Null ou un résultat indéfini.  
  
  **Exemples**  
  
  L’exemple suivant montre comment StringToNull se comporte avec différents types. 

Voici quelques exemples avec une entrée valide.

 L’espace blanc est autorisé uniquement avant ou après « null ».

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Voici quelques exemples avec une entrée non valide.

La valeur Null respecte la casse et doit être écrite avec tous les caractères en minuscules, c’est-à-dire, « null ».

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{}]
```  

L’expression passée est analysée en tant qu’expression Null ; ces entrées n’étant pas évaluées comme étant de type Null, une valeur non définie est retournée.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Retourne une expression convertie en nombre. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
 **Syntaxe**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Arguments**  
  
- `expr`  
  
   Toute expression scalaire valide à évaluer en tant qu’expression de nombre JSON. En JSON, un nombre doit être un entier ou un nombre à virgule flottante. Pour plus d’informations sur le format JSON, consultez [json.org](https://json.org/)  
  
  **Types de retour**  
  
  Retourne une expression de nombre ou un résultat indéfini.  
  
  **Exemples**  
  
  L’exemple suivant montre comment StringToNumber se comporte avec différents types. 

L’espace blanc est autorisé uniquement avant ou après le nombre.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

En JSON, un nombre valide doit être un entier ou un nombre à virgule flottante.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
{{}}
```  

L’expression passée est analysée en tant qu’expression de nombre ; ces entrées n’étant pas évaluées comme étant de type nombre, une valeur non définie est retournée. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Retourne une expression convertie en objet. Si l’expression ne peut pas être convertie, le résultat est indéfini.  
  
 **Syntaxe**  
  
```  
StringToObject(<expr>)  
```  
  
 **Arguments**  
  
- `expr`  
  
   Toute expression scalaire valide à évaluer en tant qu’expression d’objet JSON. Notez que les valeurs de chaîne imbriquées doivent être écrites avec des guillemets doubles pour être valides. Pour plus d’informations sur le format JSON, consultez [json.org](https://json.org/)  
  
  **Types de retour**  
  
  Retourne une expression d’objet ou une valeur non définie.  
  
  **Exemples**  
  
  L’exemple suivant montre comment StringToObject se comporte avec différents types. 
  
 Voici quelques exemples avec une entrée valide.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Voici le jeu de résultats obtenu.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Voici quelques exemples avec une entrée non valide.
Même s’ils sont valides dans une requête, leur analyse ne permet pas d’obtenir des objets valides. Les chaînes dans la chaîne d’objet doivent être placées dans une séquence d’échappement ("{\\"a\\":\\"str\\"}") ou les guillemets de début et de fin doivent être des guillemets simples ('{"a": "str"}').

Les guillemets simples autour des noms de propriété ne sont pas valides en JSON.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Voici le jeu de résultats obtenu.

```  
[{}]
```  

Les noms de propriété non entourés de guillemets ne sont pas valides en JSON.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Voici le jeu de résultats obtenu.

```  
[{}]
``` 

Voici quelques exemples avec une entrée non valide.

 L’expression passée est analysée en tant qu’objet JSON ; ces entrées n’étant pas évaluées comme étant de type objet, une valeur non définie est retournée.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Voici le jeu de résultats obtenu.

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 Renvoie une partie d’une expression de chaîne commençant à la position de caractère spécifiée (avec base zéro) et se poursuit jusqu'à la longueur spécifiée ou à la fin de la chaîne.  
  
 **Syntaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
- `num_expr`  
  
   Peut être toute expression numérique valide pour désigner le caractère de début et de fin.    
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant retourne la sous-chaîne de « abc » commençant à 1 pour une longueur de 1 caractère.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Retourne une représentation de chaîne d’une expression scalaire. 
  
 **Syntaxe**  
  
```  
ToString(<expr>)
```  
  
 **Arguments**  
  
- `expr`  
  
   Peut être toute expression scalaire valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment ToString se comporte avec différents types.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Étant donné l’entrée suivante :
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 L’exemple suivant illustre l’utilisation de ToString avec d’autres fonctions de chaîne telles que CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Voici le jeu de résultats obtenu.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Étant donné l’entrée suivante :
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
L’exemple suivant illustre l’utilisation de ToString avec d’autres fonctions de chaîne telles que REPLACE.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Voici le jeu de résultats obtenu.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 Renvoie une expression de chaîne après avoir supprimé les espaces de début et de fin.  
  
 **Syntaxe**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple ci-après indique comment utiliser TRIM dans une requête.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 Retourne une expression de chaîne après la conversion des caractères minuscules en caractères majuscules.  
  
 **Syntaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Arguments**  
  
- `str_expr`  
  
   Peut être toute expression de chaîne valide.  
  
  **Types de retour**  
  
  Retourne une expression de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant explique comment utiliser UPPER dans une requête  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Fonctions de tableau

Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de tableau et retournent une valeur numérique, booléenne ou de tableau :
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Retourne un tableau qui est le résultat de la concaténation d’au moins deux valeurs de tableau.  
  
 **Syntaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Arguments**  
  
- `arr_expr`  
  
   Peut être toute expression de tableau valide.  
  
  **Types de retour**  
  
  Retourne une expression de tableau.  
  
  **Exemples**  
  
  L’exemple suivant montre comment concaténer deux tableaux.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Retourne une valeur booléenne qui indique si le tableau contient la valeur spécifiée. Vous pouvez rechercher une correspondance partielle ou totale d'un objet en utilisant une expression booléenne dans la commande. 

**Syntaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Arguments**  
  
- `arr_expr`  
  
   Peut être toute expression de tableau valide.  
  
- `expr`  
  
   Peut être toute expression valide.  

- `bool_expr`  
  
   Peut être toute expression booléenne. Si la valeur est définie sur « true » et que la valeur de recherche spécifiée est un objet, la commande recherche une correspondance partielle (l'objet de la recherche est un sous-ensemble de l'un des objets). Si elle est définie sur « false », la commande recherche une correspondance totale de tous les objets du tableau. Si elle n'est pas spécifiée, la valeur par défaut est « false ». 
  
  **Types de retour**  
  
  Retourne une valeur booléenne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment vérifier l’appartenance à un tableau avec ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"b1": true, "b2": false}]  
```  

L’exemple suivant montre comment rechercher une correspondance partielle de JSON dans un tableau avec ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Retourne le nombre d’éléments de l’expression de tableau spécifiée.  
  
 **Syntaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Arguments**  
  
- `arr_expr`  
  
   Peut être toute expression de tableau valide.  
  
  **Types de retour**  
  
  Renvoie une expression numérique.  
  
  **Exemples**  
  
  L’exemple suivant comment obtenir la longueur d’un tableau avec ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Retourne une partie d’une expression de tableau.
  
 **Syntaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Arguments**  
  
- `arr_expr`  
  
   Peut être toute expression de tableau valide.  
  
- `num_expr`  
  
   Index numérique de base zéro à partir duquel commencer le tableau. Des valeurs négatives peuvent être utilisées pour spécifier l’index de départ par rapport au dernier élément du tableau, -1, par exemple, référence le dernier élément dans le tableau.  

- `num_expr`  

   Nombre maximal d’éléments dans le tableau obtenu.    

  **Types de retour**  
  
  Retourne une expression de tableau.  
  
  **Exemples**  
  
  L’exemple suivant montre comment obtenir différentes tranches d’un tableau avec ARRAY_SLICE.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Fonctions de date et heure

Les fonctions scalaires suivantes vous permettent d’obtenir les date et heure UTC actuelles sous deux formes : un horodatage numérique dont la valeur est l’époque Unix, en millisecondes, ou une chaîne conforme au format ISO 8601. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 Retourne les date et heure UTC actuelles sous forme de chaîne ISO 8601.
  
 **Syntaxe**
  
```
GETCURRENTDATETIME ()
```
  
  **Types de retour**
  
  Retourne la valeur de chaîne ISO 8601 des date et heure UTC actuelles. 

  Cette valeur est exprimée au format YYYY-MM-DDThh:mm:ss.sssZ :
  
  |||
  |-|-|
  |YYYY|Année à quatre chiffres|
  |MM|Mois à deux chiffres (01 = janvier, etc.)|
  |DD|Jour du mois à deux chiffres (01 à 31)|
  |T|Indicateur de début des éléments de l’heure|
  |hh|Heure à deux chiffres (00 à 23)|
  |MM|Minutes à deux chiffres (00 à 59)|
  |ss|Secondes à deux chiffres (00 à 59)|
  |.sss|Trois chiffres de fractions décimales d’une seconde|
  |Z|Indicateur UTC (temps universel coordonné)||
  
  Pour plus d’informations sur le format ISO 8601, consultez [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Remarques**

  GETCURRENTDATETIME est une fonction non déterministe. 
  
  Le résultat retourné est au format UTC (temps universel coordonné).

  **Exemples**  
  
  L’exemple suivant montre comment obtenir les date et heure UTC actuelles à l’aide de la fonction intégrée GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Voici un exemple de jeu de résultats.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 Retourne le nombre de millisecondes qui se sont écoulées depuis le jeudi 1er janvier 1970 à 00:00:00. 
  
 **Syntaxe**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Types de retour**  
  
  Retourne une valeur numérique représentant le nombre de millisecondes qui se sont écoulées depuis l’époque Unix, c’est-à-dire, le nombre de millisecondes qui se sont écoulées depuis le jeudi 1er janvier 1970 à 00:00:00.

  **Remarques**

  GETCURRENTTIMESTAMP est une fonction non déterministe.
  
  Le résultat retourné est au format UTC (temps universel coordonné).

  **Exemples**  
  
  L’exemple suivant montre comment obtenir l’horodatage actuel à l’aide de la fonction intégrée GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Voici un exemple de jeu de résultats.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Fonctions spatiales

Cosmos DB prend en charge les fonctions intégrées Open Geospatial Consortium (OGC) suivantes pour les requêtes géospatiales. Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur d’entrée d’objet spatial, une valeur numérique ou une valeur booléenne.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Retourne la distance entre les deux expressions Point, Polygone ou LineString de GeoJSON.  
  
 **Syntaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Arguments**  
  
- `spatial_expr`  
  
   Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
  **Types de retour**  
  
  Retourne une expression numérique contenant la distance. Elle est exprimée en mètres pour le système de référence par défaut.  
  
  **Exemples**  
  
  L’exemple suivant vous montre comment retourner tous les documents de famille se trouvant dans un rayon de 30 kilomètres de l'emplacement spécifié à l'aide de la fonction intégrée ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Retourne une expression booléenne indiquant si l’objet GeoJSON (Point, Polygone ou LineString) spécifié dans le premier argument se trouve dans le GeoJSON (Point, Polygone ou LineString) du deuxième argument.  
  
 **Syntaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Arguments**  
  
- `spatial_expr`  
  
   Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
 
- `spatial_expr`  
  
   Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
  **Types de retour**  
  
  Retourne une valeur booléenne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment rechercher tous les documents de famille d’un polygone avec ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Retourne une expression booléenne indiquant si l’objet GeoJSON (Point, Polygon ou LineString) spécifié dans le premier argument croise le GeoJSON (Point, Polygon ou LineString) du deuxième argument.  
  
 **Syntaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Arguments**  
  
- `spatial_expr`  
  
   Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
 
- `spatial_expr`  
  
   Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
  **Types de retour**  
  
  Retourne une valeur booléenne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment rechercher toutes les zones ayant une intersection avec le polygone donné.  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Renvoie une valeur booléenne indiquant si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Arguments**
  
- `spatial_expr`  
  
   Est toute expression GeoJSON Point, Polygon ou LineString valide.  
  
  **Types de retour**  
  
  Retourne une expression booléenne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment vérifier si un point est valide avec ST_VALID.  
  
  Par exemple, ce point a une valeur de latitude qui n’est pas dans la plage valide de valeurs [-90, 90]. Par conséquent, la requête retourne false.  
  
  La spécification GeoJSON impose que, pour les polygones, la dernière paire de coordonnées fournie soit identique à la première, pour créer une forme fermée. Les points dans un polygone doivent être spécifiés dans le sens antihoraire. Un polygone spécifié dans le sens horaire représente l'inverse de la région qu'il contient.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Renvoie une valeur JSON contenant une valeur booléenne si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide et, dans le cas contraire, le motif sous forme de valeur de chaîne.  
  
 **Syntaxe**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Arguments**  
  
- `spatial_expr`  
  
   Peut être toute expression GeoJSON Point ou Polygon valide.  
  
  **Types de retour**  
  
  Renvoie une valeur JSON contenant une valeur booléenne si l'expression de points ou de polygones GeoJSON spécifiée est valide et si elle est non valide, le motif sous forme de valeur de chaîne.  
  
  **Exemples**  
  
  L’exemple suivant montre comment vérifier la validité (avec détails) avec ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)
- [Agrégats](sql-query-aggregates.md)
