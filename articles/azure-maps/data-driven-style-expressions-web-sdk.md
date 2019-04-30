---
title: Données piloté par les Expressions de style dans le Kit de développement logiciel Azure Maps Web | Microsoft Docs
description: Comment utiliser des expressions de style piloté par les données dans le Kit de développement logiciel Azure Maps Web.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904917"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressions de Style piloté par les données (Kit de développement Web)

Expressions permettent d’appliquer une logique métier aux options de style qui observer les propriétés définies dans chaque forme dans une source de données. Expressions peuvent également être utilisées pour filtrer les données dans une source de données ou de la couche. Expressions peuvent se composer d’une logique conditionnelle, telles que des instructions if et peuvent également servir à manipuler des données chaîne, les opérateurs mathématiques et logiques. 

Styles orientés données peuvent réduire la quantité de code nécessaire pour implémenter une logique métier autour de style. Lorsqu’il est utilisé avec les couches, les expressions sont évaluées au moment du rendu sur un thread distinct qui fournit des performances accrues par rapport à l’évaluation d’une logique métier sur le thread d’interface utilisateur.

La vidéo suivante fournit une vue d’ensemble de l’application d’un style dans le Kit de développement logiciel Azure Maps Web orientées données.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Les expressions sont représentées sous forme de tableaux JSON. Le premier élément d’une expression dans le tableau est une chaîne qui spécifie le nom de l’opérateur d’expression. Par exemple, « + » ou « casse ». Les éléments suivants (le cas échéant) sont les arguments à l’expression. Chaque argument est soit une valeur littérale (une chaîne, le nombre, booléen, ou `null`), ou un autre tableau de l’expression. Le pseudo-code suivant définit une expression à la structure de base. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Le Kit de développement logiciel Azure Maps Web prend en charge de nombreux types d’expressions qui peuvent être utilisées sur leur propre ou en combinaison avec d’autres expressions.

| Type d’expressions | Description |
|---------------------|-------------|
| [Expressions booléennes](#boolean-expressions) | Expressions booléennes fournissent un ensemble d’expressions d’opérateurs booléens pour l’évaluation des comparaisons booléennes. |
| [Expressions de couleur](#color-expressions) | Expressions de couleur rendent plus faciles à créer et manipuler les valeurs de couleur. |
| [Expressions conditionnelles](#conditional-expressions) | Expressions conditionnelles fournissent des opérations de logique qui ressemblent à des instructions if. |
| [Expressions de données](#data-expressions) | Fournit l’accès aux données de propriété dans une fonctionnalité. |
| [Interpoler et l’étape d’expressions](#interpolate-and-step-expressions) | Interpole et expressions de l’étape peuvent être utilisées pour calculer des valeurs le long d’une fonction étape ou de la courbe interpolée. |
| [Expressions spécifiques de couche](#layer-specific-expressions) | Expressions spéciale qui sont uniquement applicables à une seule couche. |
| [Expressions mathématiques](#math-expressions) | Fournit des opérateurs mathématiques pour effectuer des calculs pilotés par les données dans le cadre de l’expression. |
| [Expressions d’opérateur de chaîne](#string-operator-expressions) | Expressions d’opérateur de chaîne effectuer des opérations de conversion de chaînes telles que la concaténation et la conversion de la casse. |
| [Expressions de type](#type-expressions) | Expressions de type fournissent des outils de test et de conversion des différents types de données tels que des chaînes, des chiffres et des valeurs booléennes. |
| [Expressions de liaison de variable](#variable-binding-expressions) | Expressions de liaison de variable permettent les résultats d’un calcul d’être stocké dans une variable et référencé dans une expression plusieurs fois sans avoir à recalculer la valeur stockée. |
| [Expression Zoom](#zoom-expression) | Récupère le niveau de zoom actuel de la carte au moment du rendu. |

Tous les exemples de ce document utilisera la fonctionnalité suivante afin d’illustrer les différentes façons, car les différents types d’expressions peuvent être utilisés. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Expressions de données

Les expressions de données fournissent un accès aux données de propriété dans une fonctionnalité. 

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['at', number, array]` | objet | Récupère un élément de tableau. |
| `['geometry-type']` | string | Obtient le type de géométrie de la fonctionnalité : Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Obtient la valeur de propriété à partir des propriétés de la fonctionnalité actuelle. Retourne la valeur null si la propriété demandée est manquante. |
| `['get', string, object]` | value | Obtient la valeur de propriété à partir des propriétés de l’objet fourni. Retourne la valeur null si la propriété demandée est manquante. |
| `['has', string]` | booléenne | Détermine si les propriétés d’une fonctionnalité ont la propriété spécifiée. |
| `['has', string, object]` | booléenne | Détermine si les propriétés de l’objet ont la propriété spécifiée. |
| `['id']` | value | Obtient les ID de la fonctionnalité si elle existe. |
| `['length', string | array]` | number | Obtient la longueur d’une chaîne ou un tableau. |

**Exemples**

Propriétés d’une fonctionnalité est accessible directement dans une expression en utilisant un `get` expression. L’exemple suivant utilise la valeur de « zoneColor » de la fonctionnalité pour spécifier la propriété color d’une couche à bulles. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

L’exemple ci-dessus fonctionne correctement si vous disposent de toutes les fonctionnalités de point le `zoneColor` propriété, mais si ce n’est pas la couleur probablement reviendra à « black ». Pour modifier la couleur de secours, un `case` expression peut être utilisée en association avec le `has` expression pour vérifier si la propriété existe, et si elle ne retourne pas une couleur de secours à la place.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Les couches en bulles et le symbole affichera les coordonnées de toutes les formes dans une source de données par défaut. Cela est possible pour mettre en évidence les sommets d’un polygone ou une ligne. Le `filter` option de la couche peut être utilisée pour limiter le type de géométrie des fonctionnalités qu’il effectue le rendu à l’aide un `['geometry-type']` expression au sein d’une expression booléenne. L’exemple suivant limite une couche à bulles afin que seuls `Point` fonctionnalités sont rendues.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

L’exemple suivant permettra à la fois `Point` et `MultiPoint` fonctionnalités doit être restitué. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

De même, le contour de polygones s’affiche dans les couches de la ligne. Pour désactiver ce comportement dans une couche de lignes, ajoutez un filtre qui autorise uniquement `LineString` et `MultiLineString` fonctionnalités.  

## <a name="math-expressions"></a>Expressions mathématiques

Les expressions mathématiques fournissent des opérateurs mathématiques pour effectuer des calculs pilotés par les données dans le cadre de l’expression.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | Calcule la somme des nombres spécifiés. |
| `['-', number]` | number | Soustrait 0 par le nombre spécifié. |
| `['-', number, number]` | number | Soustrait les premiers chiffres par le second nombre. |
| `['*', number, number, …]` | number | Multiplie les nombres spécifiés ensemble. |
| `['/', number, number]` | number | Divise le premier nombre par le second nombre. |
| `['%', number, number]` | number | Calcule le reste lors d’une division le premier nombre par le second nombre. |
| `['^', number, number]` | number | Calcule la valeur de la première valeur à la puissance du deuxième nombre. |
| `['abs', number]` | number | Calcule la valeur absolue du nombre spécifié. |
| `['acos', number]` | number | Calcule l’arc cosinus du nombre spécifié. |
| `['asin', number]` | number | Calcule l’arc sinus du nombre spécifié. |
| `['atan', number]` | number | Calcule l’arc tangente du nombre spécifié. |
| `['ceil', number]` | number | Arrondit le nombre à l’entier suivant. |
| `['cos', number]` | number | Calcule le cos du nombre spécifié. |
| `['e']` | number | Retourne la constante mathématique `e`. |
| `['floor', number]` | number | Arrondit le nombre à l’entier entière précédente. |
| `['ln', number]` | number | Calcule le logarithme népérien du nombre spécifié. |
| `['ln2']` | number | Retourne la constante mathématique `ln(2)`. |
| `['log10', number]` | number | Calcule le logarithme de base-10 du nombre spécifié. |
| `['log2', number]` | number | Calcule le logarithme de base-deux du nombre spécifié. |
| `['max', number, number, …]` | number | Calcule le nombre maximal dans le jeu spécifié de nombres. |
| `['min', number, number, …]` | number | Calcule le nombre minimal dans le jeu spécifié de nombres. |
| `['pi']` | number | Retourne la constante mathématique `PI`. |
| `['round', number]` | number | Arrondit le nombre à l’entier le plus proche. Valeurs médianes sont arrondies loin de zéro. Par exemple, `['round', -1.5]` prend la valeur -2. |
| `['sin', number]` | number | Calcule le sinus du nombre spécifié. |
| `['sqrt', number]` | number | Calcule la racine carrée du nombre spécifié. |
| `['tan', number]` | number | Calcule la tangente du nombre spécifié. |
## <a name="boolean-expressions"></a>Expressions booléennes

Expressions booléennes fournissent un ensemble d’expressions d’opérateurs booléens pour l’évaluation des comparaisons booléennes.

Lorsque vous comparez des valeurs, la comparaison est strictement typée. Valeurs de types différents sont toujours considérés comme inégaux. Les cas où les types sont connus pour être différents au moment de l’analyse sont considérés comme non valides et génère une erreur d’analyse. 

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['! ', boolean]` | booléenne | Négation logique. Retourne `true` si l’entrée est `false`, et `false` si l’entrée est `true`. |
| `['!= ', value, value]` | booléenne | Retourne `true` si les valeurs d’entrée ne sont pas égales, `false` dans le cas contraire. |
| `['<', value, value]` | booléenne | Retourne `true` si la première entrée est strictement inférieur au second, `false` dans le cas contraire. Les arguments doivent être tous deux chaînes ou les deux nombres. |
| `['<=', value, value]` | booléenne | Retourne `true` si la première entrée est inférieur ou égal au second, `false` dans le cas contraire. Les arguments doivent être tous deux chaînes ou les deux nombres. |
| `['==', value, value]` | booléenne | Retourne `true` si les valeurs d’entrée sont égales, `false` dans le cas contraire. Les arguments doivent être tous deux chaînes ou les deux nombres. |
| `['>', value, value]` | booléenne | Retourne `true` si la première entrée est strictement supérieure au second, `false` dans le cas contraire. Les arguments doivent être tous deux chaînes ou les deux nombres. |
| `['>=' value, value]` | booléenne | Retourne `true` si la première entrée est supérieur ou égal au second, `false` dans le cas contraire. Les arguments doivent être tous deux chaînes ou les deux nombres. |
| `['all', boolean, boolean, …]` | booléenne | Retourne `true` si toutes les entrées sont `true`, `false` dans le cas contraire. |
| `['any', boolean, boolean, …]` | booléenne | Retourne `true` si toutes les entrées sont `true`, `false` dans le cas contraire. |

## <a name="conditional-expressions"></a>Expressions conditionnelles

Expressions conditionnelles fournissent des opérations de logique qui ressemblent à des instructions if.

Les expressions suivantes effectuer des opérations de logique conditionnelle sur les données d’entrée. Par exemple, le `case` expression fournit la logique « if/then/else » lors de la `match` expression est comme une « instruction switch ». 

### <a name="case-expression"></a>Expression case

Un `case` expression est un type d’expression conditionnelle qui fournit l’instruction if comme une logique (if/then/else). Ce type d’expression guide tout au long d’une liste des conditions booléennes et retourne la valeur de sortie de la première condition booléenne qui a la valeur true.

Le pseudo-code suivant définit la structure de la `case` expression. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exemple**

L’exemple suivant parcourt différentes conditions booléennes jusqu'à ce qu’il en trouve une qui prend la valeur `true`et puis les retourne cette valeur associée. Si aucune condition boolean a la valeur `true`, une valeur de secours s’affichera. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Expression de correspondance

Un `match` expression est un type d’expression conditionnelle qui fournit l’instruction switch comme une logique. L’entrée peut être n’importe quelle expression comme `['get', 'entityType']` qui retourne une chaîne ou un nombre. Chaque étiquette doit être une valeur littérale unique ou un tableau de valeurs littérales, dont les valeurs doivent être toutes les chaînes ou tous les nombres. L’entrée correspond à si les valeurs correspondent dans le tableau. Chaque étiquette doit être unique. Si le type d’entrée ne correspond au type des étiquettes, le résultat sera la valeur de secours.

Le pseudo-code suivant définit la structure de la `match` expression. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exemples**

L’exemple suivant examine le `entityType` propriété d’une fonctionnalité de Point dans une couche à bulles cherche une correspondance. Si une correspondance est trouvée, incluant la valeur est retournée, ou elle retourne la valeur de secours.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

L’exemple suivant utilise un tableau pour une liste des étiquettes qui doivent retourner la même valeur. Cela est beaucoup plus efficace que de répertorier chaque étiquette individuellement. Dans ce cas, si le `entityType` propriété est « restaurant » ou « grocery_store », « rouge » s’affichera.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>L’expression de fusion

Un `coalesce` expression étapes via un ensemble d’expressions jusqu'à ce que la première valeur non null est obtenue et retourne cette valeur. 

Le pseudo-code suivant définit la structure de la `coalesce` expression. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemple**

L’exemple suivant utilise un `coalesce` expression pour définir la `textField` option d’une couche de symbole. Si le `title` propriété est manquante dans la fonctionnalité ou l’ensemble de `null`, l’expression tente alors de recherche de la `subtitle` propriété, si son manquant ou `null`, il sera puis se rabattront sur une chaîne vide. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Expressions de type

Expressions de type fournissent des outils de test et de conversion des différents types de données tels que des chaînes, des chiffres et des valeurs booléennes.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | tableau \| objet | Retourne une valeur littérale de l’objet ou tableau. Utilisez cette expression pour empêcher un tableau ou un objet soit évaluée en tant qu’expression. Cela est nécessaire quand un tableau ou un objet doit être retourné par une expression. |
| `['to-boolean', value]` | booléenne | Convertit la valeur d’entrée à une valeur booléenne. Le résultat est `false` lorsque l’entrée est une chaîne vide, `0`, `false`, `null`, ou `NaN`; sinon, sa `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Convertit la valeur d’entrée en une couleur. Si plusieurs valeurs sont fournies, chacun d’eux est évaluée dans l’ordre jusqu'à ce que la première conversion réussie est obtenue. Si aucun des entrées peut être convertie, l’expression est une erreur. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | Convertit la valeur d’entrée à un nombre, si possible. Si l’entrée est `null` ou `false`, le résultat est 0. Si l’entrée est `true`, le résultat est 1. Si l’entrée est une chaîne, il est converti en un numéro à l’aide du [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) chaîne de fonction de la spécification du langage ECMAScript. Si plusieurs valeurs sont fournies, chacun d’eux est évaluée dans l’ordre jusqu'à ce que la première conversion réussie est obtenue. Si aucun des entrées peut être convertie, l’expression est une erreur. |
| `['to-string', value]` | string | Convertit la valeur d’entrée en une chaîne. Si l’entrée est `null`, le résultat est `""`. Si l’entrée est une valeur booléenne, le résultat est `"true"` ou `"false"`. Si l’entrée est un nombre, il est converti en une chaîne à l’aide de la [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) numéro de fonction de la spécification du langage ECMAScript. Si l’entrée est une couleur, il est converti en chaîne de couleur CSS RGBA `"rgba(r,g,b,a)"`. Sinon, l’entrée est convertie en chaîne avec la [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) fonction de la spécification du langage ECMAScript. |
| `['typeof', value]` | string | Retourne une chaîne décrivant le type de la valeur donnée. |

> [!TIP]
> Si un message d’erreur similaire à `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` apparaît dans la console du navigateur signifie qu’il existe une expression quelque part dans votre code possédant un tableau qui n’a pas une chaîne pour sa première valeur. Si vous souhaitez que l’expression pour retourner un tableau, encapsuler le tableau dont le `literal` expression. L’exemple suivant définit l’icône `offset` option d’une couche de symbole, qui doit être un tableau contenant deux nombres, à l’aide un `match` expression à choisir entre deux valeurs de décalage basé sur la valeur de la `entityType` propriété du point fonctionnalité.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressions de couleur

Expressions de couleur rendent plus faciles à créer et manipuler les valeurs de couleur.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Crée une valeur de couleur à partir de *rouge*, *verte*, et *bleu* composants doivent être comprise entre `0` et `255`et un composant alpha de `1`. Si n’importe quel composant est hors limites, l’expression est une erreur. |
| `['rgba', number, number, number, number]` | color | Crée une valeur de couleur à partir de *rouge*, *verte*, *bleu* composants doivent être comprise entre `0` et `255`et un composant alpha dans une plage de `0` et `1`. Si n’importe quel composant est hors limites, l’expression est une erreur. |
| `['to-rgba']` | \[number, number, number, number\] | Retourne un tableau de quatre éléments contenant la couleur d’entrée *rouge*, *verte*, *bleu*, et *alpha* composants, dans cet ordre. |

**Exemple**

L’exemple suivant crée et la valeur de couleur RVB qui a un *rouge* valeur `255`, et *verte* et *bleu* les valeurs sont calculées en multipliant `2.5` par la valeur de la `temperature` propriété. En tant que les changements de température la couleur change différentes nuances de *rouge*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Expressions d’opérateur de chaîne

Expressions d’opérateur de chaîne effectuer des opérations de conversion de chaînes telles que la concaténation et la conversion de la casse. 

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatène plusieurs chaînes. Chaque valeur doit être une chaîne. Utilisez le `to-string` tapez l’expression à convertir d’autres types de valeur de chaîne si nécessaire. |
| `['downcase', string]` | string | Convertit la chaîne spécifiée en minuscules. |
| `['upcase', string]` | string | Convertit la chaîne spécifiée en majuscules. |

**Exemple**

L’exemple suivant convertit le `temperature` propriété du point de fonctionnalité dans une chaîne et ensuite les concatène « ° F » à la fin de celle-ci.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

L’expression ci-dessus affiche un code confidentiel sur la carte avec le texte « 64° F » superposée à elle comme indiqué dans l’image ci-dessous.

<center>

![Exemple d’expression de chaîne opérateur](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpoler et l’étape d’expressions

Interpole et expressions de l’étape peuvent être utilisées pour calculer des valeurs le long d’une fonction étape ou de la courbe interpolée. Ces expressions tenir dans une expression qui retourne une valeur numérique en tant que leur entrée, par exemple `['get',  'temperature']`. La valeur d’entrée est recherchée dans les paires de valeurs d’entrée et de sortie, appelées « s’arrête », pour déterminer la valeur qui convient le mieux à la fonction de courbe ou étape interpolée. Les valeurs d’entrée pour chaque arrêt doivent être un nombre et être dans l’ordre croissant. Les valeurs de sortie doivent être un nombre et tableau de nombres ou une couleur.

### <a name="interpolate-expression"></a>Interpoler expression

Un `interpolate` expression peut être utilisée pour calculer un ensemble continu et sans heurts de valeurs par l’interpolation entre les valeurs de mots vides. Un `interpolate` expression qui retourne des valeurs de couleur produit un dégradé de couleur de résultats les valeurs sont sélectionnées à partir de.

Il existe trois types de méthodes d’interpolation qui peuvent être utilisés dans un `interpolate` expression :
 
* `['linear']` -Effectue une interpolation linéaire entre la paire de s’arrête.
* `['exponential', base]` -D’interpolation de façon exponentielle entre les taquets de. Le `base` valeur contrôle le taux auquel l’augmentation de la sortie. Valeurs supérieures mettent la sortie à augmenter de plus vers la fin de la plage. Un `base` valeur proche de 1 produit une sortie qui augmente de façon plus linéaire.
* `['cubic-bezier', x1, y1, x2, y2]` -Effectue une interpolation à l’aide un [courbe de Bézier cubique](https://developer.mozilla.org/docs/Web/CSS/timing-function) défini par les points de contrôle donné.

Voici un exemple de l’aspect de ces différents types d’interpolations. 

| Linéaire  | Exponentielle | De Bézier cubique |
|---------|-------------|--------------|
| ![Graphique de l’interpolation linéaire](media/how-to-expressions/linear-interpolation.png) | ![Graphique de l’interpolation exponentielle](media/how-to-expressions/exponential-interpolation.png) | ![Graphique de l’interpolation de Bézier cubique](media/how-to-expressions/bezier-curve-interpolation.png) |

Le pseudo-code suivant définit la structure de la `interpolate` expression. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exemple**

L’exemple suivant utilise un `linear interpolate` expression pour définir le `color` basée sur les propriétés d’une couche à bulles sur la `temperature` propriété de la fonctionnalité de point. Si le `temperature` valeur est inférieure à 60, « bleu » est retournée, si entre 60 et inférieur à 70, jaune s’affichera, si entre 70 et inférieur à 80, « orange » sera retourné, si 80 ou supérieur, « rouge » est renvoyé.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

L’illustration suivante montre la façon dont les couleurs sont choisis pour l’expression ci-dessus.
 
<center>

![Interpoler exemple d’expression](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Expression d’étape

Un `step` expression peut être utilisée pour calculer des valeurs de résultat discrètes, en escalier en évaluant un [par morceaux constante fonction](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) défini par s’arrête. 

Le pseudo-code suivant définit la structure de la `step` expression. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Les expressions d’étape retournent la valeur de sortie de l’arrêt juste avant la valeur d’entrée, ou la première valeur d’entrée si l’entrée est inférieure à la première étape. 

**Exemple**

L’exemple suivant utilise un `step` expression pour définir le `color` basée sur les propriétés d’une couche à bulles sur la `temperature` propriété de la fonctionnalité de point. Si le `temperature` valeur est inférieure à 60, « bleu » est retournée, si entre 60 et inférieur à 70, « jaune » est retournée, si entre 70 et inférieur à 80, « orange » sera retourné, si 80 ou supérieur, « rouge » est renvoyé.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

L’illustration suivante montre la façon dont les couleurs sont choisis pour l’expression ci-dessus.
 
<center>

![Exemple d’expression étape](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressions spécifiques de couche

Expressions spéciale qui s’appliquent uniquement aux couches spécifiques.

### <a name="heat-map-density-expression"></a>Expression de densité de carte thermique

Une expression de densité de carte thermique récupère la valeur de densité de carte thermique pour chaque pixel dans une couche de carte thermique et est définie comme `['heatmap-density']`. Cette valeur est un nombre compris entre `0` et `1` et est utilisé en association avec un `interpolation` ou `step` expression pour définir le dégradé de couleur utilisé pour mettre en couleur de la carte thermique. Cette expression peut uniquement être utilisée dans le [option de couleur](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) de la couche de carte thermique.

> [!TIP]
> La couleur à l’index 0 dans une expression d’interpolation ou la couleur par défaut d’une couleur de l’étape, définit la couleur de la zone où il n’existe aucune donnée et peut être utilisé pour définir une couleur d’arrière-plan. La plupart des utilisateurs préfèrent définir cette valeur sur une couleur noire transparente ou semi-transparente. 

**Exemple**

Cet exemple utilise une expression d’interpolation chemise pour créer un dégradé de couleurs pour le rendu de la carte thermique. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

En plus de l’utilisation d’un dégradé lisse à mettre en couleur d’une carte thermique, couleurs peuvent être spécifiés dans un ensemble de plages à l’aide un `step` expression. À l’aide un `step` expression pour la colorisation de la carte thermique décompose la densité visuellement en plages plus semblable à une carte de style de contour ou en radar.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Pour plus d’informations, consultez le [ajouter une couche de carte thermique](map-add-heat-map-layer.md) documentation.

### <a name="line-progress-expression"></a>Expression de progression de ligne

Une expression de progression ligne récupère la progression le long d’un trait dégradé dans une couche de lignes et qu’il est définie comme `['line-progress']`. Cette valeur est un nombre compris entre 0 et 1 et est utilisée en association avec un `interpolation` ou `step` expression. Cette expression peut uniquement être utilisée avec le [strokeGradient option]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) de la couche de lignes. 

> [!NOTE]
> Le `strokeGradient` nécessite l’option de la couche de lignes de la `lineMetrics` option de la source de données à définir `true`.

**Exemple**

L’exemple suivant utilise le `['line-progress']` expression pour appliquer un dégradé de couleur au trait d’une ligne.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Consultez l’exemple en direct](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expression de format de champ de texte

L’expression de format de champ de texte peut être utilisée avec le `textField` option des couches de symbole `textOptions` propriété afin de fournir la mise en forme du texte mixte. Cette expression permet un ensemble de chaînes d’entrée et de mise en forme d’options à spécifier. Les options suivantes peuvent être spécifiées pour chaque chaîne d’entrée dans cette expression.

 * `'font-scale'` -Spécifie le facteur d’échelle pour la taille de police. Si spécifié, cette valeur remplace la `size` propriété de la `textOptions` pour la chaîne individuelle.
 * `'text-font'` -Spécifie un ou plusieurs familles de polices qui doivent être utilisés pour cette chaîne. Si spécifié, cette valeur remplace la `font` propriété de la `textOptions` pour la chaîne individuelle.

Le pseudo-code suivant définit la structure de l’expression de format de champ de texte. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Exemple**

L’exemple suivant met en forme le champ de texte en ajoutant une police en gras et en augmentant la taille de police de la `title` propriété de la fonctionnalité. Cet exemple ajoute également le `subtitle` propriété de la fonctionnalité sur un saut de ligne, avec une mise à l’échelle vers le bas de la taille de police.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Cette couche s’affiche à la fonctionnalité de point, comme illustré dans l’image ci-dessous :
 
<center>

![Image de la fonctionnalité de Point avec un champ de texte mis en forme](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Expression de numéro de format

Le `number-format` expression peut uniquement être utilisée avec la `textField` option d’une couche de symbole. Cette expression convertit le nombre fourni en une chaîne mise en forme. Cette expression encapsule de JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) de fonction et prend en charge l’ensemble suivant d’options.

 * `locale` -Spécifier cette option pour convertir des nombres en chaînes d’une manière qui s’aligne avec la langue spécifiée. Passer un [balise de langue BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) dans cette option.
 * `currency` -Convertir le nombre en une chaîne représentant une devise. Les valeurs possibles sont les [les codes de devise ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), tels que « USD » pour le dollar américain, « Euros » de l’euro, ou « CNY » pour le chinois RMB.
 * `'min-fraction-digits'` -Spécifie le nombre minimal de décimales à inclure dans la version de chaîne du nombre.
 * `'max-fraction-digits'` -Spécifie le nombre maximal de décimales à inclure dans la version de chaîne du nombre.

Le pseudo-code suivant définit la structure de l’expression de format de champ de texte. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exemple**

L’exemple suivant utilise un `number-format` expression pour modifier la `revenue` propriété de la fonctionnalité de point est rendu dans le `textField` option d’un symbole de couche s’afficher une valeur en dollars américains.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Cette couche s’affiche à la fonctionnalité de point, comme illustré dans l’image ci-dessous :

<center>

![Exemple d’expression de format numérique](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Expression Zoom

Un `zoom` expression est utilisée pour récupérer le niveau de zoom actuel de la carte au moment du rendu et est définie comme `['zoom']`. Cette expression retourne un nombre entre la plage de niveau de zoom minimale et maximale de la carte. À l’aide de cette expression autorise les styles à modifier dynamiquement que le niveau de zoom de la carte est modifié. Le `zoom` expression peut uniquement être utilisée avec `interpolate` et `step` expressions.

**Exemple**

Par défaut, les rayons des points de données restitués dans la couche de carte thermique ont un rayon de pixel fixe pour tous les niveaux de zoom. Comme la carte est agrandi ou réduite l’ensemble des agrégats de données et la couche de carte thermique est différente. Un `zoom` expression peut être utilisée pour mettre à l’échelle le rayon pour chaque niveau de zoom de telle sorte que chaque point de données couvre la même zone physique de la carte. Cela rendra la couche de carte thermique rechercher plus statique et cohérente. Chaque niveau de zoom de la carte a deux fois en tant que nombre de pixels verticalement et horizontalement en tant que le niveau de zoom précédent. Mise à l’échelle le rayon de sorte qu’elle double avec chaque niveau de zoom créera une carte thermique qui ont l’air cohérente sur tous les niveaux de zoom. Cela est possible à l’aide de la `zoom` expression avec un `base 2 exponential interpolation` expression comme indiqué ci-dessous. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Consultez l’exemple en direct](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressions de liaison de variable

Expressions de liaison de variable de stocker les résultats d’un calcul dans une variable afin qu’il peut être référencé ailleurs dans une expression plusieurs fois sans avoir à recalculer. Il s’agit d’une optimisation utile pour les expressions qui impliquent beaucoup de calculs

| Expression | Type de retour | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom1 : chaîne,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1 : tout,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2 : chaîne,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2 : tout,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Stocke une ou plusieurs valeurs en tant que variables pour une utilisation par le `var` dans l’expression enfant qui retourne le résultat. |
| `['var', name: string]` | any | Fait référence à une variable qui a été créée à l’aide de la `let` expression. |

**Exemple**

Cet exemple utilise une expression qui calcule le chiffre d’affaires par rapport à des taux de température, puis utilise un `case` expression à évaluer les différentes opérations booléennes sur cette valeur. Le `let` expression est utilisée pour stocker le chiffre d’affaires par rapport à des taux de température afin qu’il ne doit être calculée une fois et le `var` expression fait référence à cette variable aussi souvent que nécessaire sans avoir à recalculer.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour d’autres exemples de code qui implémentent des expressions :

> [!div class="nextstepaction"] 
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Ajouter des formes](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Ajouter une couche de carte thermique](map-add-heat-map-layer.md)

En savoir plus sur les options de couche qui prennent en charge des expressions :

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
