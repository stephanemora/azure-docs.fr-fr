---
title: Créer des stratégies pour les propriétés de tableau sur des ressources
description: Apprenez à gérer des paramètres de tableau et des expressions de langage de tableau, à évaluer l’alias [*] et à ajouter des éléments avec des règles de définition de stratégie Azure.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: d4e059f3691554aa91dfd15cf308ef62afa58928
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089965"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Créer des stratégies pour les propriétés de tableau sur des ressources Azure

Les propriétés Azure Resource Manager sont généralement définies comme des chaînes et des valeurs booléennes. Lorsqu’il existe une relation un-à-plusieurs, des propriétés complexes sont plutôt définies comme des tableaux. Dans Azure Policy, les tableaux sont utilisés de différentes manières :

- Le type d’un [paramètre de définition](../concepts/definition-structure.md#parameters), pour fournir plusieurs options
- Une partie d’une [règle de stratégie](../concepts/definition-structure.md#policy-rule) utilisant les conditions **In** ou **notIn**
- Partie d’une règle de stratégie qui compte le nombre de membres d’un tableau qui remplissent une condition
- Dans les effets [append](../concepts/effects.md#append) et [modify](../concepts/effects.md#modify) pour mettre à jour un tableau existant

Cet article traite chaque utilisation par Azure Policy et fournit plusieurs exemples de définitions.

## <a name="parameter-arrays"></a>Tableaux de paramètres

### <a name="define-a-parameter-array"></a>Définir un tableau de paramètres

La définition d’un paramètre sous forme de tableau permet une flexibilité de la stratégie lorsque plusieurs valeurs sont nécessaires.
Cette définition de stratégie permet de régler n’importe quel emplacement unique pour le paramètre **allowedLocations** et les valeurs par défaut sur _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Comme le **type** était _chaîne_, une seule valeur peut être définie lors de l’affectation de la stratégie. Si cette stratégie est affectée, les ressources dans l’étendue sont autorisées uniquement dans une seule région Azure. La plupart des définitions de stratégies doivent autoriser une liste des options approuvées, par exemple pour autoriser _eastus2_, _eastus_, et _westus2_.

Pour créer la définition de stratégie visant à autoriser plusieurs options, utilisez le **type** _tableau_. La même stratégie peut être réécrite comme suit :

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Lorsqu’une définition de stratégie est enregistrée, la propriété **type** sur un paramètre ne peut pas être modifiée.

Cette nouvelle définition de paramètre accepte plusieurs valeurs lors de l’affectation de stratégie. Avec la propriété de tableau **allowedValues** définie, les valeurs disponibles lors de l’affectation sont limitées à la liste prédéfinie de choix. L’utilisation de **allowedValues** est facultative.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passer des valeurs à un tableau de paramètres lors de l’attribution

Lors de l’affectation de la stratégie via le portail Azure, un paramètre de **type** _tableau_ s’affiche sous la forme d’une zone de texte unique. L’indicateur indique « Utiliser ’;’ pour séparer des valeurs. (par exemple, Londres;New York) ». Pour passer les valeurs d’emplacement autorisées de _eastus2_, _eastus_ et _westus2_ au paramètre, utilisez la chaîne suivante :

`eastus2;eastus;westus2`

Le format de la valeur du paramètre est différent lorsque vous utilisez Azure CLI, Azure PowerShell ou l’API REST. Les valeurs sont transmises via une chaîne JSON qui inclut également le nom du paramètre.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Pour utiliser cette chaîne avec chaque kit SDK, utilisez les commandes suivantes :

- Azure CLI : Commande [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) avec le paramètre **params**
- Azure PowerShell : Applet de commande [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) avec le paramètre **PolicyParameter**
- API REST : Dans l’opération _PUT_ [create](/rest/api/resources/policyassignments/create) en tant que partie intégrante du corps de la demande en tant que valeur d’une propriété **properties.parameters**

## <a name="using-arrays-in-conditions"></a>Utilisation de tableaux dans des conditions

### <a name="in-and-notin"></a>In et notIn

Les conditions `in` et `notIn` ne fonctionnent qu’avec des valeurs de tableau. Elles vérifient l’existence d’une valeur dans un tableau. Le tableau peut être un tableau JSON littéral ou une référence à un paramètre de tableau. Par exemple :

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>value count

L’expression [value count](../concepts/definition-structure.md#value-count) compte le nombre d’expressions qui remplissent une condition. Elle offre un moyen d’évaluer plusieurs fois la même condition, en utilisant des valeurs différentes pour chaque itération. Par exemple, la condition suivante vérifie si le nom de la ressource correspond à un modèle dans un tableau de modèles :

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Pour évaluer l’expression, Azure Policy évalue la condition `where` trois fois, une fois pour chaque membre de `[ "test*", "dev*", "prod*" ]`, en comptant le nombre de fois où elle a été jugée `true`. À chaque itération, la valeur du membre du tableau actuel est associée au nom d’index `pattern` défini par `count.name`. Cette valeur peut ensuite être référencée à l’intérieur de la condition `where` en appelant une fonction de modèle spéciale : `current('pattern')`.

| Itération | valeur renvoyée `current('pattern')` |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

La condition est vraie uniquement si le nombre obtenu est supérieur à 0.

Pour rendre la condition ci-dessus plus générique, utilisez une référence de paramètre au lieu d’un tableau littéral :

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Lorsque l’expression **value count** ne figure pas sous une autre expression **count**, `count.name` est facultative, et la fonction `current()` peut être utilisée sans argument :

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

**valeur count** prend également en charge les tableaux d’objets complexes, ce qui permet des conditions plus complexes. Par exemple, la condition suivante définit une valeur de balise souhaitée pour chaque modèle de nom, et vérifie si le nom de la ressource correspond au modèle, mais n’a pas la valeur de balise requise :

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Pour obtenir des exemples utiles, consultez [Exemples value count](../concepts/definition-structure.md#value-count-examples).

## <a name="referencing-array-resource-properties"></a>Référencement des propriétés de ressource de tableau

De nombreux cas d’usage requièrent l’utilisation de propriétés de tableau dans la ressource évaluée. Certains scénarios requièrent le référencement d’un tableau entier (par exemple, la vérification de sa longueur). D’autres requièrent l’application d’une condition à chaque membre du tableau (par exemple, s’assurer que toutes les règles de pare-feu bloquent l’accès à partir d’Internet). Comprendre les différentes façons dont Azure Policy peut faire référence aux propriétés de ressources et comment ces références se comportent lorsqu’elles font référence à des propriétés de tableau est la clé pour écrire les conditions qui couvrent ces scénarios.

### <a name="referencing-resource-properties"></a>Référencement des propriétés de ressource

Les propriétés de ressource peuvent être référencées par Azure Policy à l’aide d’[alias](../concepts/definition-structure.md#aliases). Il existe deux façons de référencer les valeurs d’une propriété de ressource dans Azure Policy :

- Utilisez la condition [field](../concepts/definition-structure.md#fields) pour vérifier si **toutes** les propriétés de ressource sélectionnées remplissent une condition. Exemple :

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Utilisez la fonction `field()` pour accéder à la valeur d’une propriété. Exemple :

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

La condition field a un comportement implicite « Tous ». Si l’alias représente une collection de valeurs, il vérifie si toutes les valeurs individuelles remplissent la condition. La fonction `field()` retourne les valeurs représentées par l’alias telles quelles, qui peuvent ensuite être manipulées par d’autres fonctions de modèle.

### <a name="referencing-array-fields"></a>Référencement des champs de tableau

Les propriétés de ressource de tableau sont généralement représentées par deux différents types d’alias. Un alias « normal » et des [alias de tableau](../concepts/definition-structure.md#understanding-the--alias) auxquels `[*]` est attaché :

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Référencement du tableau

Le premier alias représente une valeur unique, la valeur de la propriété `stringArray` provenant du contenu de la demande. Étant donné que la valeur de cette propriété est un tableau, elle n’est pas utile dans les conditions de stratégie. Par exemple :

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Cette condition compare l’intégralité du tableau `stringArray` à une valeur de chaîne unique. La plupart des conditions, notamment `equals`, acceptent uniquement des valeurs de chaîne. Il n’est donc pas très utile de comparer un tableau à une chaîne. Le principal scénario où il est utile de référencer la propriété du tableau est lorsqu’il s’agit de vérifier si cette propriété existe :

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Avec la fonction `field()`, la valeur renvoyée est le tableau du contenu de la demande, qui peut ensuite être utilisé avec l’une des [fonctions de modèle prises en charge](../concepts/definition-structure.md#policy-functions) qui acceptent des arguments de tableau. Par exemple, la condition suivante vérifie si la longueur de `stringArray` est supérieure à 0 :

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Référencement de la collection des membres du tableau

Les alias qui utilisent la syntaxe `[*]` représentent une **collection de valeurs de propriété sélectionnées à partir d’une propriété de tableau**, ce qui est différent de la sélection de la propriété de tableau proprement dite. Dans le cas de `Microsoft.Test/resourceType/stringArray[*]`, elle renvoie une collection qui contient tous les membres de `stringArray`. Comme mentionné précédemment, une condition `field` vérifie que toutes les propriétés de ressource sélectionnées remplissent la condition. Par conséquent, la condition suivante est vraie uniquement si **tous** les membres de `stringArray` sont égaux à la « valeur ».

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Si le tableau contient des objets, un alias `[*]` peut être utilisé pour sélectionner la valeur d’une propriété spécifique de chaque membre du tableau. Exemple :

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Cette condition est vraie si les valeurs de toutes les propriétés `property` dans `objectArray` sont égales à `"value"`. Pour obtenir plus d’exemples, consultez [Autres exemples d’alias \[\*\]](#additional--alias-examples).

Lors de l’utilisation de la fonction `field()` pour référencer un alias de tableau, la valeur renvoyée est un tableau de toutes les valeurs sélectionnées. Ce comportement signifie que le cas d’usage courant de la fonction `field()`, c’est-à-dire la possibilité d’appliquer des fonctions de modèle à des valeurs de propriété de ressource, est limité. Les seules fonctions de modèle qui peuvent être utilisées dans ce cas sont celles qui acceptent des arguments de tableau. Par exemple, il est possible d’obtenir la longueur du tableau avec `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]`. Toutefois, des scénarios plus complexes, tels que l’application d’une fonction de modèle à chaque membre du tableau et sa comparaison à une valeur souhaitée, sont possibles uniquement en utilisant l’expression `count`. Pour plus d’informations, consultez [Expression field count](#field-count-expressions).

Pour résumer, consultez l’exemple de contenu de ressource suivant et les valeurs sélectionnées renvoyées par différents alias :

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Lorsque vous utilisez la condition field sur l’exemple de contenu de ressource, les résultats sont les suivants :

| Alias | Valeurs sélectionnées |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Collection vide de valeurs. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Collection vide de valeurs. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Lorsque vous utilisez la fonction `field()` sur l’exemple de contenu de ressource, les résultats sont les suivants :

| Expression | Valeur renvoyée |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Expressions field count

Les expressions [field count](../concepts/definition-structure.md#field-count) comptent le nombre de membres de tableau qui remplissent une condition et le comparent à une valeur cible. L’expression `Count` est plus intuitive et polyvalente pour l’évaluation des tableaux par rapport aux conditions `field`. La syntaxe est :

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

En cas d’utilisation sans condition `where`, `count` retourne simplement la longueur d’un tableau. Avec l’exemple de contenu de ressource de la section précédente, l’évaluation de l’expression `count` suivante est `true` puisque `stringArray` a trois membres :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Ce comportement fonctionne également avec les tableaux imbriqués. Par exemple, l’expression `count` suivante est évaluée comme `true` dans la mesure où il y a quatre membres de tableau dans les tableaux `nestedArray` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

La puissance de `count` tient à la condition `where`. Quand elle est spécifiée, Azure Policy énumère les membres du tableau et évalue chacun d’entre eux par rapport à la condition, en comptant le nombre de membres du tableau évalués comme `true`. Plus précisément, dans chaque itération de l’évaluation de la condition `where`, Azure Policy sélectionne un membre de tableau unique ***i** _ et évalue le contenu de la ressource par rapport à la condition `where` _*comme si ***i**_ était le seul membre du tableau_*. Le fait d’avoir un seul membre de tableau disponible dans chaque itération permet d’appliquer des conditions complexes sur chaque membre du tableau.

Exemple :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```

Pour évaluer l’expression `count`, Azure Policy évalue la condition `where` trois fois, une fois pour chaque membre de `stringArray`, en comptant le nombre de fois où elle a été jugée `true`.
Lorsque la condition `where` fait référence aux membres du tableau `Microsoft.Test/resourceType/stringArray[*]`, au lieu de sélectionner tous les membres de `stringArray`, elle ne sélectionne qu’un seul membre de tableau à chaque fois :

| Itération | Valeurs `Microsoft.Test/resourceType/stringArray[*]` sélectionnées | Résultat de l’évaluation `where` |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

`count` retourne `1`.

Voici une expression plus complexe :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Itération | Valeurs sélectionnées | Résultat de l’évaluation `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

`count` retourne `1`.

Le fait que l’expression `where` soit évaluée par rapport à la **totalité** du contenu de la requête (avec des modifications apportées uniquement au membre du tableau en cours d’énumération) signifie que la condition `where` peut également faire référence à des champs en dehors du tableau :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| Itération | Valeurs sélectionnées | Résultat de l’évaluation `where` |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Des expressions count imbriquées peuvent être utilisées pour appliquer des conditions aux champs de tableau imbriqués. Par exemple, la condition suivante vérifie que le tableau `objectArray[*]` a exactement deux membres avec `nestedArray[*]` qui contient un ou plusieurs membres :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Itération | Valeurs sélectionnées | Résultat de l’évaluation count imbriquée |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` a 2 membres => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` a 2 membres => `true` |

Étant donné que les deux membres de `objectArray[*]` ont un tableau enfant `nestedArray[*]` avec deux membres, l’expression outer count retourne `2`.

Exemple plus complexe : vérifiez que le tableau `objectArray[*]` a exactement deux membres avec `nestedArray[*]` avec tous les membres égaux à `2` ou `3` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Itération | Valeurs sélectionnées | Résultat de l’évaluation count imbriquée
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` contient `2` => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` contient `3` => `true` |

Étant donné que les deux membres de `objectArray[*]` ont un tableau enfant `nestedArray[*]` qui contient `2` ou `3`, l’expression outer count retourne `2`.

> [!NOTE]
> Les expressions count de champs imbriqués peuvent seulement faire référence à des tableaux imbriqués. Par exemple, l’expression count faisant référence à `Microsoft.Test/resourceType/objectArray[*]` peut avoir une expression count imbriquée ciblant le tableau imbriqué `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]`, mais elle ne peut pas avoir d’expression count imbriquée ciblant `Microsoft.Test/resourceType/stringArray[*]`.

#### <a name="accessing-current-array-member-with-template-functions"></a>Accès au membre du tableau actuel avec des fonctions de modèle

Quand vous utilisez des fonctions de modèle, utilisez la fonction `current()` pour accéder à la valeur du membre du tableau actuel ou aux valeurs de l’une de ses propriétés. Pour accéder à la valeur du membre du tableau actuel, passez l’alias défini dans `count.field` ou l’un de ses alias enfants comme argument à la fonction `current()` . Par exemple :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Itération | valeur renvoyée `current()` | Résultat de l’évaluation `where` |
|:---|:---|:---|
| 1 | La valeur de `property` dans le premier membre de `objectArray[*]` : `value1` | `true` |
| 2 | La valeur de `property` dans le premier membre de `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>La fonction field dans des conditions where

La fonction `field()` peut également être utilisée pour accéder à la valeur du membre du tableau actuel tant que l’expression **count** ne figure pas à l’intérieur d’une **condition d’existence** (la fonction`field()` fait toujours référence à la ressource évaluée dans la condition **if**). Le comportement de `field()` lorsque vous faites référence au tableau évalué repose sur les concepts suivants :

1. Les alias de tableau sont résolus en une collection de valeurs sélectionnées parmi tous les membres du tableau.
1. Les fonctions `field()` qui référencent des alias de tableau retournent un tableau avec les valeurs sélectionnées.
1. Le référencement de l’alias de tableau compté à l’intérieur de la condition `where` retourne une collection avec une seule valeur sélectionnée dans le membre du tableau évalué dans l’itération actuelle.

Ce comportement signifie que, lorsque vous faites référence au membre de tableau compté avec une fonction `field()` à l’intérieur de la condition `where`, **un tableau avec un seul membre est renvoyé**. Bien que ce comportement ne soit pas intuitif, c’est cohérent avec l’idée que les alias de tableau renvoient toujours une collection de propriétés sélectionnées. Voici un exemple :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Itération | Valeurs d’expressions | Résultat de l’évaluation `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Par conséquent, lorsqu’il est nécessaire d’accéder à la valeur de l’alias de tableau compté avec une fonction `field()`, la façon de le faire consiste à l’inclure dans un wrapper avec une fonction de modèle `first()` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Itération | Valeurs d’expressions | Résultat de l’évaluation `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Pour obtenir des exemples utiles, consultez [Exemples field count](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Modification de tableaux

Les effets [append](../concepts/effects.md#append) et [modify](../concepts/effects.md#modify) modifient les propriétés d’une ressource lors de sa création ou de sa mise à jour. Lorsque vous utilisez des propriétés de tableau, le comportement de ces effets varie selon que l’opération tente de modifier ou non l’alias **\[\*\]**  :

> [!NOTE]
> L’utilisation de l’effet `modify` avec des alias est actuellement disponible en **préversion**.

|Alias |Résultat | Résultat |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy ajoute l’ensemble du tableau spécifié dans les détails de l’effet le cas échéant. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` avec l’opération `add` | Azure Policy ajoute l’ensemble du tableau spécifié dans les détails de l’effet le cas échéant. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` avec l’opération `addOrReplace` | Azure Policy ajoute l’ensemble du tableau spécifié dans les détails de l’effet le cas échéant ou remplace le tableau existant. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy ajoute le membre du tableau spécifié dans les détails de l’effet. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` avec l’opération `add` | Azure Policy ajoute le membre du tableau spécifié dans les détails de l’effet. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` avec l’opération `addOrReplace` | Azure Policy supprime tous les membres de tableau existants et ajoute le membre de tableau spécifié dans les détails de l’effet. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy ajoute une valeur à la propriété `action` de chaque membre du tableau. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` avec l’opération `add` | Azure Policy ajoute une valeur à la propriété `action` de chaque membre du tableau. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` avec l’opération `addOrReplace` | Azure Policy ajoute ou remplace la propriété `action` de chaque membre du tableau. |

Pour plus d’informations, consultez ces [exemples Append](../concepts/effects.md#append-examples).

## <a name="additional--alias-examples"></a>Autres exemples d’alias [*]

Il est recommandé d’utiliser les [expressions field count](#field-count-expressions) pour vérifier si « tout » ou « partie » des membres d’un tableau dans le contenu de la requête remplissent une condition. Mais dans certaines conditions simples, il est possible d’obtenir le même résultat en utilisant un accesseur de champ avec un alias de tableau, comme décrit dans [Référencement de la collection des membres du tableau](#referencing-the-array-members-collection). Ce modèle être utile dans des règles de stratégie qui dépassent la limite du nombre d’expressions **count**. Voici des exemples de cas d’utilisation courants :

L’exemple de règle de stratégie pour la table de scénario ci-dessous :

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Pour la table de scénario ci-dessous, le tableau des **ipRules** se présente de la façon suivante :

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Pour chaque exemple de condition ci-dessous, remplacez `<field>` par `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Les résultats suivants viennent de la combinaison de la condition et de l’exemple de règle de stratégie et le tableau des valeurs existantes ci-dessus :

|Condition |Résultat | Scénario |Explication |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Rien |Aucun ne correspond |Un élément du tableau est évalué comme false (127.0.0.1 ! = 127.0.0.1) et un autre comme true (127.0.0.1 ! = 192.168.1.1), donc la condition **notEquals** est _false_ et l’effet n’est pas déclenché. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effet de la stratégie |Aucun ne correspond |Les éléments du tableau sont évalués comme true (10.0.4.1 != 127.0.0.1 et 10.0.4.1 != 192.168.1.1), donc la condition **notEquals** est _true_ et l’effet est déclenché. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effet de la stratégie |Un ou plusieurs correspondent |Un élément du tableau est évalué comme false (127.0.0.1 != 127.0.0.1) et un autre comme true (127.0.0.1 != 192.168.1.1), donc la condition **notEquals** est _false_. L’opérateur logique est évalué comme true (**non** _false_), de sorte que l’effet est déclenché. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Rien |Un ou plusieurs correspondent |Les deux éléments du tableau sont évalués comme false (10.0.4.1 != 127.0.0.1 et 10.0.4.1 != 192.168.1.1), donc la condition **notEquals** est _true_. L’opérateur logique est évalué comme false (**non** _true_), de sorte que l’effet n’est pas déclenché. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effet de la stratégie |Tous ne correspondent pas |Un élément du tableau est évalué comme true (127.0.0.1 == 127.0.0.1) et un autre comme false (127.0.0.1 == 192.168.1.1), donc la condition **Equals** est _false_. L’opérateur logique est évalué comme true (**non** _false_), de sorte que l’effet est déclenché. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effet de la stratégie |Tous ne correspondent pas |Les deux éléments du tableau sont évalués comme false (10.0.4.1 == 127.0.0.1 et 10.0.4.1 == 192.168.1.1), donc la condition **Equals** est _false_. L’opérateur logique est évalué comme true (**non** _false_), de sorte que l’effet est déclenché. |
|`{<field>,"Equals":"127.0.0.1"}` |Rien |Tous correspondent |Un élément du tableau est évalué comme true (127.0.0.1 == 127.0.0.1) et un autre comme true (127.0.0.1 == 192.168.1.1), donc la condition **Equals** est _false_ et l’effet n’est pas déclenché. |
|`{<field>,"Equals":"10.0.4.1"}` |Rien |Tous correspondent |Les deux éléments du tableau sont évalués comme false (10.0.4.1 == 127.0.0.1 et 10.0.4.1 == 192.168.1.1), donc la condition **Equals** est _false_ et l’effet n’est pas déclenché. |

## <a name="next-steps"></a>Étapes suivantes

- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [corriger des ressources non conformes](remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).
