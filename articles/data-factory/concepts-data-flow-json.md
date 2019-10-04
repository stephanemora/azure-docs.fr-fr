---
title: Concepts JSON de la fonctionnalité de mappage de flux de données dans Azure Data Factory
description: Le mappage de flux de données Data Factory intègre des fonctionnalités pour la gestion des documents JSON à l’aide de hiérarchies
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: cde42dda47d54c03c50895bc625f99c9350b53e3
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210511"
---
# <a name="mapping-data-flow-json-handling"></a>Gestion JSON du mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>Création de structures JSON dans l’éditeur d’expressions
### <a name="derived-column-transformation"></a>Transformation de colonne dérivée
L’ajout d’une colonne complexe à votre flux de données est plus facile via l’éditeur d’expressions de la colonne dérivée. Après l’ajout d’une nouvelle colonne et l’ouverture de l’éditeur, vous disposez de deux options : entrer la structure JSON manuellement ou utiliser l’interface utilisateur pour ajouter des sous-colonnes de manière interactive.

#### <a name="interactive-ui-json-design"></a>Conception JSON via l’interface utilisateur interactive
Dans le volet latéral du schéma de sortie, de nouvelles sous-colonnes peuvent être ajoutées à l’aide du menu `+` : ![Ajouter une sous-colonne](media/data-flow/addsubcolumn.png "Ajouter une sous-colonne")

À partir de là, de nouvelles colonnes et sous-colonnes peuvent être ajoutées de la même façon. Pour chaque champ non complexe, une expression peut être ajoutée dans l’éditeur d’expressions vers la droite.

![Colonne complexe](media/data-flow/complexcolumn.png "Colonne complexe")

#### <a name="manual-json-design"></a>Conception JSON manuelle
Pour ajouter manuellement une structure JSON, ajoutez une nouvelle colonne et entrez l’expression dans l’éditeur. L’expression suit le format général suivant :
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Si cette expression était entrée pour une colonne nommée « complexColumn », elle serait écrite dans le récepteur sous la forme du code JSON suivant :
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-dsl"></a>Exemple de code DSL manuel
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Options de format source
### <a name="default"></a>Default
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Document individuel
* Option 1
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Option 2
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Noms de colonnes sans guillemets
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Comporte des commentaires
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Apostrophes simples
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Barres obliques inverses d’échappement
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

# <a name="higher-order-functions"></a>Fonctions d’ordre supérieur
## <a name="filter"></a>filter
Filtre hors du tableau les éléments qui ne respectent pas le prédicat fourni. Filter attend une référence à un élément dans la fonction de prédicat comme #item.

### <a name="examples"></a>Exemples
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Mappe chaque élément du tableau à un nouvel élément à l’aide de l’expression fournie. Map attend une référence à un élément dans la fonction d’expression comme #item.

### <a name="examples"></a>Exemples
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>reduce
Accumule des éléments dans un tableau. Reduce attend une référence à un accumulateur et un élément dans la première fonction d’expression comme #acc et #item et il attend que la valeur résultante comme #result soit utilisée dans la deuxième fonction d’expression.

### <a name="examples"></a>Exemples
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
Trie le tableau à l’aide de la fonction de prédicat fournie. Sort attend une référence à deux éléments consécutifs dans la fonction d’expression comme #item1 et #item2.

### <a name="examples"></a>Exemples
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
Retourne true si un élément quelconque du tableau fourni correspond à true dans le prédicat fourni. Contains attend une référence à un élément dans la fonction de prédicat comme #item.

### <a name="examples"></a>Exemples
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser la transformation de colonne dérivée pour générer vos structures hiérarchiques](data-flow-derived-column.md)
