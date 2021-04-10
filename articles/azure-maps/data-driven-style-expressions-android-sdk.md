---
title: Expressions de style basées sur les données dans les cartes Android | Microsoft Azure Maps
description: En savoir plus sur les expressions de style basées sur les données. Découvrez comment utiliser ces expressions dans le Kit de développement logiciel (SDK) Android Azure Maps pour ajuster les styles dans les cartes.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097412"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Expressions de style basé sur les données (SDK Android)

Les expressions vous permettent d’appliquer une logique métier à des options de style qui observent les propriétés définies dans chaque forme d’une source de données. Les expressions peuvent filtrer des données dans une source de données ou une couche. Les expressions peuvent se composer d’une logique conditionnelle, comme des instructions if. Elles peuvent aussi être utilisées pour manipuler les données à l’aide d’opérateurs de chaîne, d’opérateurs logiques et d’opérateurs mathématiques.

Les styles basés sur les données réduisent la quantité de code nécessaire pour implémenter une logique métier autour des styles. Lorsqu’elles sont utilisées avec des couches, les expressions sont évaluées au moment du rendu sur un thread distinct. Cette fonctionnalité offre des performances accrues par rapport à l’évaluation de la logique métier sur le thread d’interface utilisateur.

Le SDK Android Azure Maps prend en charge quasiment toutes les mêmes expressions de style que le SDK web Azure Maps. Ainsi, les mêmes concepts que ceux décrits dans [Expressions de style basées sur les données (SDK web)](data-driven-style-expressions-web-sdk.md) peuvent être reportés dans une application Android. Toutes les expressions de style dans le SDK Android Azure Maps sont disponibles sous l’espace de noms `com.microsoft.azure.maps.mapcontrol.options.Expression`. Il existe de nombreux types d’expressions de style.

| Type d’expressions | Description |
|---------------------|-------------|
| [Expressions booléennes](#boolean-expressions) | Les expressions booléennes fournissent un ensemble d’expressions d’opérateurs booléens pour l’évaluation de comparaisons booléennes. |
| [Expressions de couleur](#color-expressions) | Les expressions de couleur simplifient la création et la manipulation de valeurs de couleurs. |
| [Expressions conditionnelles](#conditional-expressions) | Les expressions conditionnelles fournissent des opérations de logique qui ressemblent à des instructions if. |
| [Expressions de données](#data-expressions) | Permet d’accéder aux données de propriété dans une fonctionnalité. |
| [Expressions interpolate et step](#interpolate-and-step-expressions) | Les expressions interpolate et step peuvent être utilisées pour calculer des valeurs le long d’une courbe interpolée ou d’une fonction d’étape. |
| [Expressions JSON](#json-based-expressions) | Facilite la réutilisation des expressions JSON brutes créées pour le kit de développement logiciel (SDK) web avec le SDK Android. |  
| [Expressions spécifiques à une couche](#layer-specific-expressions) | Expressions spéciale qui ne sont applicables qu’à une couche unique. |
| [Expressions mathématiques](#math-expressions) | Fournit des opérateurs mathématiques pour effectuer des calculs basés sur les données dans le framework de l’expression. |
| [Expressions d’opérateur de chaîne](#string-operator-expressions) | Les expressions d’opérateur de chaîne effectuent des opérations de conversion sur des chaînes, comme une concaténation et une conversion de la casse. |
| [Expressions du type](#type-expressions) | Les expressions du type fournissent des outils permettant de tester et de convertir différents types de données comme des chaînes, des nombres et des valeurs booléennes. |
| [Expressions de liaison de variable](#variable-binding-expressions) | Les expressions de liaison de variables stockent les résultats d’un calcul dans une variable et les référencent plusieurs fois ailleurs dans une expression sans avoir à recalculer la valeur stockée. |
| [Expression zoom](#zoom-expression) | Récupère le niveau de zoom actuel de la carte au moment de la restitution. |

> [!NOTE]
> La syntaxe des expressions est globalement identique dans Java et Kotlin. Si, alors que la documentation est définie sur Kotlin, vous voyez des blocs de code pour Java, le code est identique dans les deux langages.

Tous les exemples de cette section du document utilisent la fonctionnalité suivante pour illustrer diverses façons d’utiliser ces expressions.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

Le code suivant montre comment créer manuellement cette fonctionnalité GeoJSON dans une application.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

Le code suivant montre comment désérialiser la version convertie en chaîne de l’objet JSON en une fonctionnalité GeoJSON dans une application.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>Expressions JSON

Le kit de développement logiciel (SDK) web Azure Maps prend également en charge les expressions de style basées sur les données qui sont représentées par un tableau JSON. Ces mêmes expressions peuvent être recréées à l’aide de la classe `Expression` native dans le SDK Android. Ces expressions JSON peuvent également être converties en une chaîne à l’aide d’une fonction web telle que `JSON.stringify` et passées dans la méthode `Expression.raw(String rawExpression)`. Par exemple, prenez l’expression JSON suivante.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

La version convertie en chaîne de l’expression ci-dessus serait `"['get','title']"` et peut être lue dans le SDK Android comme suit.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

L’utilisation de cette approche peut faciliter la réutilisation d’expressions de style entre les applications mobiles et web qui utilisent Azure Maps.

Cette vidéo fournit une vue d’ensemble des styles basés sur les données dans Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Expressions de données

Les expressions de données permettent d’accéder aux données de propriété dans une fonctionnalité.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `accumulated()` | nombre | Obtient la valeur d’une propriété de cluster accumulée jusqu’à présent. |
| `at(number | Expression, Expression)` | value | Récupère un élément à partir d’un tableau. |
| `geometryType()` | string | Obtient le type de géométrie de la fonctionnalité : Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | Obtient la valeur de propriété à partir des propriétés de l’objet fourni. Retourne la valeur null si la propriété demandée est manquante. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Détermine si les propriétés d’une fonctionnalité ont la propriété spécifiée. |
| `id()` | value | Obtient l’ID de la fonctionnalité, le cas échéant. |
| `in(string | number | Expression, Expression)` | boolean | Détermine si un élément existe dans un tableau |
| `length(string | Expression)` | nombre | Obtient la longueur d’une chaîne ou d’un tableau. |
| `properties()`| value | Obtient l’objet de propriétés de la fonctionnalité. |

Les expressions de style du kit de développement logiciel (SDK) web suivantes ne sont pas prises en charge dans le SDK Android :

- index-of
- slice

**Exemples**

Les propriétés d’une fonctionnalité sont accessibles directement dans une expression en utilisant une expression `get`. Cet exemple utilise la valeur `zoneColor` de la fonctionnalité pour spécifier la propriété color d’une couche de bulles.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

L’exemple ci-dessus fonctionne correctement si toutes les fonctionnalités de point sont dotées de la propriété `zoneColor`. Si ce n’est pas le cas, la couleur de secours sera probablement « black » (noir). Pour modifier la couleur de secours, utilisez une expression `switchCase` en association avec l’expression `has` pour vérifier si la propriété existe. Si la propriété n’existe pas, retournez une couleur de secours.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

Les couches de bulles et de symboles affichent par défaut les coordonnées de toutes les formes d’une source de données. Ce comportement peut mettre en évidence les sommets d’un polygone ou une ligne. L’option `filter` de la couche peut être utilisée pour limiter le type de géométrie des fonctionnalités qu’elle restitue à l’aide d’une expression `geometryType` dans une expression booléenne. L’exemple suivant limite une couche de bulles afin que seules les fonctionnalités `Point` soient restituées.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

L’exemple suivant permet la restitution des fonctionnalités `Point` et `MultiPoint`.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

De même, le contour des polygones s’affichera dans les couches de lignes. Pour désactiver ce comportement dans une couche de lignes, ajoutez un filtre qui autorise uniquement les fonctionnalités `LineString` et `MultiLineString`.  

Voici quelques exemples supplémentaires d’utilisation des expressions de données :

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Expressions mathématiques

Les expressions mathématiques fournissent des opérateurs mathématiques pour effectuer des calculs basés sur les données dans le framework de l’expression.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `abs(number | Expression)` | nombre | Calcule la valeur absolue du nombre spécifié. |
| `acos(number | Expression)` | nombre | Calcule l’arc cosinus du nombre spécifié. |
| `asin(number | Expression)` | nombre | Calcule l’arc sinus du nombre spécifié. |
| `atan(number | Expression)` | nombre | Calcule l’arc tangente du nombre spécifié. |
| `ceil(number | Expression)` | nombre | Arrondit le nombre à l’entier supérieur suivant. |
| `cos(number | Expression)` | nombre | Calcule le cosinus du nombre spécifié. |
| `division(number, number)` \| `division(Expression, Expression)` | nombre | Divise le premier nombre par le deuxième nombre. Expression équivalente du SDK web : `/` |
| `e()` | nombre | Retourne la constante mathématique `e`. |
| `floor(number | Expression)` | nombre | Arrondit le nombre à l’entier inférieur précédent. |
| `log10(number | Expression)` | nombre | Calcule le logarithme en base dix du nombre spécifié. |
| `log2(number | Expression)` | nombre | Calcule le logarithme en base deux du nombre spécifié. |
| `ln(number | Expression)` | nombre | Calcule le logarithme naturel du nombre spécifié. |
| `ln2()` | nombre | Retourne la constante mathématique `ln(2)`. |
| `max(numbers... | expressions...)` | nombre | Calcule le nombre maximal dans l’ensemble spécifié de nombres. |
| `min(numbers... | expressions...)` | nombre | Calcule le nombre minimal dans l’ensemble spécifié de nombres. |
| `mod(number, number)` \| `mod(Expression, Expression)` | nombre | Calcule le reste de la division du premier nombre par le deuxième. Expression équivalente du SDK web : `%` |
| `pi()` | nombre | Retourne la constante mathématique `PI`. |
| `pow(number, number)` \| `pow(Expression, Expression)` | nombre | Calcule la valeur du premier nombre élevé à la puissance du deuxième nombre. |
| `product(numbers... | expressions...)` | nombre | Multiplie les nombres spécifiés entre eux. Expression équivalente du SDK web : `*` |
| `round(number | Expression)` | nombre | Arrondit le nombre à l’entier le plus proche. Les valeurs médianes sont arrondies à la valeur la plus éloignée de zéro. Par exemple, `round(-1.5)` a pour résultat `-2`. |
| `sin(number | Expression)` | nombre | Calcule le sinus du nombre spécifié. |
| `sqrt(number | Expression)` | nombre | Calcule la racine carrée du nombre spécifié. |
| `subtract(number | Expression` | nombre | Retranche 0 du nombre spécifié. |
| `subtract(number | Expression, number | Expression)` | nombre | Retranche les premiers nombres du deuxième nombre. |
| `sum(numbers... | expressions...)` | nombre | Calcule la somme des nombres spécifiés. |
| `tan(number | Expression)` | nombre | Calcule la tangente du nombre spécifié. |

## <a name="boolean-expressions"></a>Expressions booléennes

Les expressions booléennes fournissent un ensemble d’expressions d’opérateurs booléens pour l’évaluation de comparaisons booléennes.

Lors de la comparaison de valeurs, la comparaison est strictement typée. Les valeurs de types différents sont toujours considérées comme inégales. Les cas où les types sont reconnus différents au moment de l’analyse sont considérés comme non valides et génèrent une erreur d’analyse.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Retourne `true` si toutes les entrées ont la valeur `true` ; retourne `false` dans le cas contraire. |
| `any(Expression...)` | boolean | Retourne `true` si l’une des entrées a la valeur `true` ; retourne `false` si ce n’est pas le cas. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retourne `true` si les valeurs d’entrée sont égales ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retourne `true` si la première entrée est strictement supérieure à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retourne `true` si la première entrée est supérieure ou égale à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retourne `true` si la première entrée est strictement inférieure à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retourne `true` si la première entrée est inférieure ou égale à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Retourne `true` si les valeurs d’entrée ne sont pas égales ; `false` dans le cas contraire. |
| `not(Expression | boolean)` | boolean | Négation logique. Retourne `true` si l’entrée est `false`, et `false` si l’entrée est `true`. |

## <a name="conditional-expressions"></a>Expressions conditionnelles

Les expressions conditionnelles fournissent des opérations de logique qui ressemblent à des instructions if.

Les expressions suivantes effectuent des opérations de logique conditionnelle sur les données d’entrée. Par exemple, l’expression `switchCase` fournit la logique « if/then/else » alors que l’expression `match` est comme une « instruction switch ».

### <a name="switch-case-expression"></a>Expression case simple

Une expression `switchCase` est un type d’expression conditionnelle qui fournit une logique « if/then/else ». Ce type d’expression parcourt une liste de conditions booléennes. Elle retourne la valeur de sortie de la première condition booléenne à évaluer sur true.

Le pseudo-code suivant définit la structure de l’expression `switchCase`.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Exemple**

L’exemple suivant parcourt différentes conditions booléennes jusqu’à ce qu’il en trouve une qui a pour résultat `true`, puis il retourne cette valeur associée. Si aucune condition booléenne n’a pour résultat `true`, une valeur de secours est retournée.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Expression match

Une expression `match` est un type d’expression conditionnelle qui fournit une logique semblable à l’instruction switch. L’entrée peut être n’importe quelle expression, par exemple `get( "entityType")`, qui retourne une chaîne ou un nombre. Chaque arrêt doit avoir une étiquette qui est une valeur littérale unique ou un tableau de valeurs littérales dont les valeurs doivent être toutes des chaînes ou toutes des nombres. L’entrée est une correspondance si l’une des valeurs du tableau correspond. Chaque étiquette d’arrêt doit être unique. Si le type d’entrée ne correspond au type des étiquettes, le résultat est la valeur de secours par défaut.

Le pseudo-code suivant définit la structure de l’expression `match`.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Exemples**

L’exemple suivant examine la propriété `entityType` d’une fonctionnalité Point dans une couche de bulles afin de trouver une correspondance. Si une correspondance est trouvée, la valeur spécifiée est retournée ; sinon, il retourne la valeur de secours.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

L’exemple suivant utilise un tableau pour lister un ensemble d’étiquettes qui doivent toutes retourner la même valeur. Cette approche est beaucoup plus efficace que répertorier chaque étiquette individuellement. Dans ce cas, si la propriété `entityType` a la valeur « restaurant » ou « grocery_store », la couleur « red » (rouge) est retournée.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>Expression coalesce

Une expression `coalesce` parcourt un ensemble d’expressions jusqu’à ce que la première valeur non null soit obtenue, puis retourne cette valeur.

Le pseudo-code suivant définit la structure de l’expression `coalesce`.

```java
coalesce(Expression... input)
```

**Exemple**

L’exemple suivant utilise une expression `coalesce` pour définir l’option `textField` d’une couche de symboles. Si la propriété `title` est manquante dans la fonctionnalité ou qu’elle est définie sur `null`, l’expression tente alors de rechercher la propriété `subTitle` ; si elle est manquante ou `null`, elle utilise une chaîne vide.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Expressions du type

Les expressions du type fournissent des outils permettant de tester et de convertir différents types de données comme des chaînes, des nombres et des valeurs booléennes.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `array(Expression)` | Object[] | Déclare que l’entrée est un tableau. |
| `bool(Expression)` | boolean | Déclare que la valeur d’entrée est une valeur booléenne. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | collator | Retourne un collator à utiliser dans les opérations de comparaison dépendantes des paramètres régionaux. La valeur par défaut des options case-sensitive et diacritic-sensitive est false. L’argument locale spécifie l’étiquette de langue IETF des paramètres régionaux à utiliser. Si aucune valeur n’est fournie, les paramètres régionaux par défaut sont utilisés. Si les paramètres régionaux demandés ne sont pas disponibles, le collator utilise les paramètres régionaux de base définis par le système. Utilisez resolved-locale pour tester les résultats du comportement des paramètres régionaux de base.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | boolean \| number \| string \| Object \| Object[] | Retourne une valeur littérale d’objet ou de tableau. Utilisez cette expression pour empêcher qu’un tableau ou un objet soit évalué en tant qu’expression. Cela est nécessaire quand un tableau ou un objet doit être retourné par une expression. |
| `number(Expression)` | nombre | Déclare que la valeur d’entrée est un nombre. |
| `object(Expression)` | Object | Déclare que la valeur d’entrée est un objet. |
| `string(Expression)` | string | Déclare que la valeur d’entrée est une chaîne. |
| `toArray(Expression)` | Object[] | Convertit l’expression en un tableau d’objets JSON. |
| `toBool(Expression)` | boolean | Convertit la valeur d’entrée en une valeur booléenne. |
| `toNumber(Expression)` | nombre | Convertit la valeur d’entrée en un nombre, si cela est possible. |
| `toString(Expression)` | string | Convertit la valeur d’entrée en une chaîne. |
| `typeoOf(Expression)` | string | Retourne une chaîne décrivant le type de la valeur donnée. |

## <a name="color-expressions"></a>Expressions de couleur

Les expressions de couleur simplifient la création et la manipulation de valeurs de couleurs.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `color(int)` | color | Convertit une valeur entière de couleur en une expression de couleur. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Crée une valeur de couleur à partir des composants *red*, *green* et *blue* dont les valeurs doivent être comprises entre `0` et `255`, et d’un composant alpha ayant la valeur `1`. Si l’un des composants est hors limites, l’expression est une erreur. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Crée une valeur de couleur à partir des composants *red*, *green*, *blue* dont les valeurs doivent être comprises entre `0` et `255`, et d’un composant alpha dont la valeur est comprise entre `0` et `1`. Si l’un des composants est hors limites, l’expression est une erreur. |
| `toColor(Expression)` | color  | Convertit la valeur d’entrée en une couleur. |
| `toRgba(Expression)` | color | Retourne un tableau à quatre éléments contenant les composants red, green, blue et alpha de la couleur d’entrée, dans cet ordre. |

**Exemple**

L’exemple suivant crée une valeur de couleur RVB qui a une valeur *red* égale à `255` et des valeurs *green* et *blue* calculées en multipliant `2.5` par la valeur de la propriété `temperature`. Quand la température change, la couleur est remplacée par différentes nuances de rouge (*red*).

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Si tous les paramètres de couleur sont des nombres, il n’est pas nécessaire de les encapsuler avec l’expression `literal`. Par exemple :

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> Les valeurs de couleur de chaîne peuvent être converties en couleur à l’aide de la méthode `android.graphics.Color.parseColor`. La commande suivante convertit une chaîne de couleur hexadécimale en expression de couleur qui peut être utilisée avec une couche.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Expressions d’opérateur de chaîne

Les expressions d’opérateur de chaîne effectuent des opérations de conversion sur des chaînes, comme une concaténation et une conversion de la casse.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | Concatène plusieurs chaînes. Chaque valeur doit être une chaîne. Utilisez l’expression du type `toString` pour convertir d’autres types de valeurs en chaîne, si nécessaire. |
| `downcase(string)` \| `downcase(Expression)` | string | Convertit la chaîne spécifiée en minuscules. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Détermine si la chaîne d’entrée utilise un jeu de caractères pris en charge par la pile de polices actuelle. Par exemple : `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | Retourne l’étiquette de langue IETF des paramètres régionaux utilisés par le collator fourni. Vous pouvez l’utiliser pour déterminer les paramètres régionaux par défaut du système ou pour savoir si les paramètres régionaux demandés ont été correctement chargés. |
| `upcase(string)` \| `upcase(Expression)` | string | Convertit la chaîne spécifiée en majuscules. |

**Exemple**

L’exemple suivant convertit la propriété `temperature` de la fonctionnalité de point en chaîne, puis les concatène « °F » à la fin de celle-ci.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

L’expression ci-dessus affiche une épingle sur la carte avec le texte « 64 °F » superposé, comme illustré dans l’image ci-dessous.

![Exemple d’expression d’opérateur de chaîne](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions&quot;></a>Expressions interpolate et step

Les expressions interpolate et step peuvent être utilisées pour calculer des valeurs le long d’une courbe interpolée ou d’une fonction d’étape. Ces expressions prennent une expression qui retourne une valeur numérique comme entrée ; par exemple, `get(&quot;temperature")`. La valeur d’entrée est évaluée par rapport à des paires de valeurs d’entrée et de sortie pour déterminer la valeur qui convient le mieux à la fonction de courbe interpolée ou d’étape. Les valeurs de sortie sont appelées « arrêts » (stops). Les valeurs d’entrée de chaque arrêt doivent être un nombre et être dans l’ordre croissant. Les valeurs de sortie doivent être un nombre et un tableau de nombres, ou une couleur.

### <a name="interpolate-expression"></a>Expression interpolate

Une expression `interpolate` peut être utilisée pour calculer un ensemble régulier et continu de valeurs en effectuant des interpolations entre des valeurs d’arrêt. Une expression `interpolate` qui retourne des valeurs de couleur produit un dégradé de couleurs dans lequel les valeurs de résultats sont sélectionnées. L’expression `interpolate` a les formats suivants :

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Il existe trois types de méthodes d’interpolation qui peuvent être utilisées dans une expression `interpolate` :

| Nom | Description |
|------|-------------|
| `linear()` | Effectue une interpolation de manière linéaire entre la paire d’arrêts.  |
| `exponential(number)` \| `exponential(Expression)` | Effectue une interpolation de façon exponentielle entre les arrêts. Une « base » est spécifiée et contrôle le taux auquel la sortie augmente. Les valeurs élevées font davantage augmenter la sortie vers l’extrémité supérieure de la plage. Une valeur de « base » proche de 1 produit une sortie qui augmente de façon plus linéaire.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Effectue une interpolation à l’aide d’une [courbe de Bézier cubique](https://developer.mozilla.org/docs/Web/CSS/timing-function) définie par les points de contrôle donnés. |

Le format de l’expression `stop` est `stop(stop, value)`.

Voici un exemple de ce à quoi ressemblent ces différents types d’interpolations.

| Linéaire  | Exponentielle | De Bézier cubique |
|---------|-------------|--------------|
| ![Graphique d’interpolation linéaire](media/how-to-expressions/linear-interpolation.png) | ![Graphique d’interpolation exponentielle](media/how-to-expressions/exponential-interpolation.png) | ![Graphique d’interpolation de Bézier cubique](media/how-to-expressions/bezier-curve-interpolation.png) |

**Exemple**

L’exemple suivant utilise une expression `linear interpolate` pour définir la propriété `bubbleColor` d’une couche de bulles sur la propriété `temperature` de la fonctionnalité de point. Si la valeur `temperature` est inférieure à 60, « blue » est retournée. Si elle est comprise entre 60 et 70, « yellow » est retournée. Si elle est comprise entre 70 et 80, « orange » (`#FFA500`) est retournée. Si elle est supérieure ou égale à 80, « red » est retournée.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

L’image suivante montre comment les couleurs sont choisies pour l’expression ci-dessus.

![Exemple d’expression interpolate](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Expression step

Une expression `step` peut être utilisée pour calculer des valeurs de résultat discrètes échelonnées en évaluant une [fonction constante par morceaux](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) définie par des arrêts.

L’expression `interpolate` a les formats suivants :

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Les expressions step retournent la valeur de sortie de l’arrêt juste avant la valeur d’entrée, ou la première valeur d’entrée si l’entrée est inférieure au premier arrêt.

**Exemple**

L’exemple suivant utilise une expression `step` pour définir la propriété `bubbleColor` d’une couche de bulles sur la propriété `temperature` de la fonctionnalité de point. Si la valeur `temperature` est inférieure à 60, « blue » est retournée. Si elle est comprise entre 60 et 70, « yellow » est retournée. Si elle est comprise entre 70 et 80, « orange » est retournée. Si elle est supérieure ou égale à 80, « red » est retournée.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

L’image suivante montre comment les couleurs sont choisies pour l’expression ci-dessus.

![ step](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Expressions spécifiques à la couche

Expressions spéciales qui s’appliquent uniquement à des couches spécifiques.

### <a name="heat-map-density-expression"></a>Expression de densité de carte thermique

Une expression de densité de carte thermique, qui est définie sous la forme `heatmapDensity`, récupère la valeur de densité de carte thermique pour chaque pixel d’une couche de carte thermique. Cette valeur est un nombre compris entre `0` et `1`. Elle est utilisée en combinaison avec une expression `interpolation` ou `step` pour définir le dégradé de couleurs utilisé pour mettre en couleur la carte thermique. Cette expression peut uniquement être utilisée dans l’option `heatmapColor` de la couche de carte thermique.

> [!TIP]
> La couleur à l’index 0 dans une expression d’interpolation ou la couleur par défaut d’une expression d’étape définit la couleur des zones où il n’existe aucune donnée. La couleur à l’index 0 peut être utilisée pour définir une couleur d’arrière-plan. La plupart des utilisateurs préfèrent définir cette valeur sur une couleur noire transparente ou semi-transparente.

**Exemple**

Cet exemple utilise une expression d’interpolation linéaire afin de créer un dégradé de couleurs léger pour afficher la carte thermique.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

En plus de l’utilisation d’un dégradé léger pour mettre en couleur une carte thermique, les couleurs peuvent être spécifiées dans un ensemble de plages à l’aide d’une expression `step`. L’utilisation d’une expression `step` pour coloriser la carte thermique décompose visuellement la densité en plages. Elle ressemble ainsi davantage à une carte de style contour ou radar.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

Pour plus d’informations, consultez la documentation [Ajouter une couche de carte thermique](map-add-heat-map-layer-android.md).

### <a name="line-progress-expression"></a>Expression de progression des lignes

Une expression de progression des lignes, qui est définie sous la forme `lineProgress()`, récupère la progression le long d’une ligne avec dégradé dans une couche de lignes. Cette valeur est un nombre compris entre 0 et 1. Elle est utilisée en association avec une expression `interpolation` ou `step`. Cette expression ne peut être utilisée qu’avec l’option `strokeGradient` de la couche de lignes.

> [!NOTE]
> L’option `strokeGradient` de la couche de lignes exige que l’option `lineMetrics` de la source de données soit définie sur `true`.

**Exemple**

Cet exemple utilise l’expression `lineProgress()` pour appliquer un dégradé de couleurs au trait d’une ligne.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Consulter un exemple en direct](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expression de format de champ de texte

L’expression `format` peut être utilisée avec l’option `textField` des couches de symboles afin de fournir une mise en forme mixte du texte. Cette expression accepte une ou plusieurs expressions `formatEntry` qui spécifient une chaîne et un ensemble de `formatOptions` à ajouter au champ de texte.

| Expression | Description |
|------------|-------------|
| `format(Expression...)` | Retourne du texte mis en forme contenant des annotations pour une utilisation dans des entrées de champ de texte de format mixte. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Retourne une entrée de chaîne mise en forme pour une utilisation dans l’expression `format`. |

Les options de format suivantes sont disponibles :

| Expression | Description |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Spécifie le facteur d’échelle pour la taille de police. Si elle est spécifiée, cette valeur remplace la propriété `textSize` pour la chaîne individuelle. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Spécifie une couleur à appliquer à un texte lors du rendu. |

**Exemple**

L’exemple suivant met en forme le champ de texte en ajoutant une police gras et en effectuant un scale-up de la taille de police de la propriété `title` de la fonctionnalité. Cet exemple ajoute également la propriété `subTitle` de la fonctionnalité sur une nouvelle ligne, avec un scale-down de la taille de police.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Cette couche affiche la fonctionnalité de point, comme illustré dans l’image ci-dessous :

![Image de la fonctionnalité Point avec un champ de texte mis en forme](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Expression zoom

Une expression `zoom`, qui est définie sous la forme `zoom()`, est utilisée pour récupérer le niveau de zoom actuel de la carte au moment de la restitution. Cette expression retourne un nombre compris entre la plage de niveau de zoom minimale et maximale de la carte. Les contrôles de la carte interactive Azure Maps pour le web et Android prennent en charge 25 niveaux de zoom, numérotés de 0 à 24. L’utilisation de l’expression `zoom` permet la modification dynamique des styles à mesure que le niveau de zoom de la carte est modifié. L’expression `zoom` peut uniquement être utilisée avec des expressions `interpolate` et `step`.

**Exemple**

Par défaut, les rayons des points de données restitués dans la couche de carte thermique ont un rayon de pixels fixe pour tous les niveaux de zoom. Alors que la carte fait l’objet d’un zoom, les données sont agrégées, et la couche de carte thermique change d’aspect. Une expression `zoom` peut être utilisée pour mettre à l’échelle le rayon de chaque niveau de zoom de sorte que chaque point de données couvre la même zone physique de la carte. Elle fait paraître la couche de carte thermique plus statique et plus cohérente. Chaque niveau de zoom de la carte a deux fois plus de pixels verticalement et horizontalement que le niveau de zoom précédent. La mise à l’échelle du rayon, de sorte qu’il soit multiplié par deux avec chaque niveau de zoom, crée une carte thermique qui paraît cohérente à tous les niveaux de zoom. Vous pouvez le faire en utilisant l’expression `zoom` avec une expression `base 2 exponential interpolation`, avec le rayon de pixels défini pour le niveau de zoom minimal et un rayon mis à l’échelle pour le niveau de zoom maximal calculé en tant que `2 * Math.pow(2, minZoom - maxZoom)`, comme indiqué ci-dessous.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Expressions de liaison de variable

Les expressions de liaison de variables stockent les résultats d’un calcul dans une variable. Ainsi, les résultats du calcul peuvent être référencés ailleurs dans une expression à plusieurs reprises. Il s’agit d’une optimisation utile pour les expressions qui impliquent de nombreux calculs.

| Expression | Type de retour | Description |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Stocke une ou plusieurs valeurs sous forme de variables pour une utilisation par l’expression `var` dans l’expression enfant qui retourne le résultat. |
| `var(Expression expression)` \| `var(string variableName)` | Object | Fait référence à une variable qui a été créée à l’aide de l’expression `let`. |

**Exemple**

Cet exemple utilise une expression qui calcule le chiffre d’affaires par comparaison avec un rapport de température, puis utilise une expression `case` pour évaluer différentes opérations booléennes sur cette valeur. L’expression `let` est utilisée pour stocker le chiffre d’affaires par rapport au ratio de température, de sorte qu’il ne doive être calculé qu’une seule fois. L’expression `var` fait référence à cette variable aussi souvent que nécessaire sans avoir à la recalculer.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les couches qui prennent en charge des expressions :

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une carte thermique](map-add-heat-map-layer-android.md)
