---
title: JSON dans le flux de données de mappage
description: Le mappage de flux de données Azure Data Factory intègre des fonctionnalités pour la gestion des documents JSON à l’aide de hiérarchies
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/30/2019
ms.openlocfilehash: 153c7a1003c68526c960644bebcc4800e92edc3c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928324"
---
# <a name="mapping-data-flow-json-handling"></a>Gestion JSON du mappage de flux de données

## <a name="creating-json-structures-in-derived-column"></a>Création de structures JSON dans une colonne dérivée

Vous pouvez ajouter une colonne complexe à votre flux de données à l’aide du générateur d’expressions de la colonne dérivée. Dans la transformation de colonne dérivée, ajoutez une nouvelle colonne et ouvrez le générateur d’expressions en cliquant sur la zone bleue. Pour rendre une colonne complexe, vous pouvez entrer la structure JSON manuellement ou utiliser l’expérience utilisateur pour ajouter des sous-colonnes de manière interactive.

### <a name="using-the-expression-builder-ux"></a>Utilisation de l’expérience utilisateur pour le générateur d’expressions

Dans le volet latéral du schéma de sortie, pointez sur une colonne, puis cliquez sur l’icône plus. Sélectionnez **Ajouter une sous-colonne** pour transformer la colonne en type complexe.

![Ajouter une sous-colonne](media/data-flow/addsubcolumn.png "Ajouter une sous-colonne")

Vous pouvez ajouter des colonnes et des sous-colonnes supplémentaires de la même façon. Pour chaque champ non complexe, une expression peut être ajoutée dans l’éditeur d’expressions vers la droite.

![Colonne complexe](media/data-flow/complexcolumn.png "Colonne complexe")

### <a name="entering-the-json-structure-manually"></a>Saisie manuelle de la structure JSON

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exemple de script manuel pour une définition hiérarchique complète
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

L’utilisation d’un jeu de données JSON comme source dans votre flux de données vous permet de définir cinq paramètres supplémentaires. Ces paramètres se trouvent sous l’accordéon de **paramètres JSON**, sous l’onglet **Options de la source**.  

![Paramètres JSON](media/data-flow/json-settings.png "Paramètres JSON")

### <a name="default"></a>Default

Par défaut, les données JSON sont lues dans le format suivant.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Document individuel

Si **Document unique** est sélectionné, le mappage de flux de données lit un document JSON à partir de chaque fichier. 

``` json
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

Si **Noms de colonnes sans guillemets** est sélectionné, le mappage des flux de données lit les colonnes JSON qui ne sont pas entourées de guillemets. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Comporte des commentaires

Sélectionnez **Comporte des commentaires** si les données JSON ont des commentaires de style C ou C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Apostrophes simples

Sélectionnez **Apostrophes simples** si les champs et valeurs JSON utilisent des guillemets simples au lieu de guillemets doubles.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Barres obliques inverses d’échappement

Sélectionnez **Apostrophes simples** si les barres obliques inverses sont utilisées pour échapper les caractères dans les données JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Fonctions d’ordre supérieur

Une fonction d’ordre supérieur est une fonction qui accepte une ou plusieurs fonctions comme arguments. Vous trouverez ci-dessous une liste des fonctions d’ordre supérieur prises en charge dans le mappage des flux de données qui permettent d’effectuer des opérations de tableau.

### <a name="filter"></a>filter
Filtre hors du tableau les éléments qui ne respectent pas le prédicat fourni. Filter attend une référence à un élément dans la fonction de prédicat comme #item.

#### <a name="examples"></a>Exemples
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Mappe chaque élément du tableau à un nouvel élément à l’aide de l’expression fournie. Map attend une référence à un élément dans la fonction d’expression comme #item.

#### <a name="examples"></a>Exemples
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>reduce
Accumule des éléments dans un tableau. Reduce attend une référence à un accumulateur et un élément dans la première fonction d’expression comme #acc et #item et il attend que la valeur résultante comme #result soit utilisée dans la deuxième fonction d’expression.

#### <a name="examples"></a>Exemples
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sort
Trie le tableau à l’aide de la fonction de prédicat fournie. Sort attend une référence à deux éléments consécutifs dans la fonction d’expression comme #item1 et #item2.

#### <a name="examples"></a>Exemples
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
Retourne true si un élément quelconque du tableau fourni correspond à true dans le prédicat fourni. Contains attend une référence à un élément dans la fonction de prédicat comme #item.

#### <a name="examples"></a>Exemples
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser la transformation de colonne dérivée pour générer vos structures hiérarchiques](data-flow-derived-column.md)
