---
title: Transformation de fractionnement conditionnel dans un flux de données de mappage Azure Data Factory
description: Fractionnez les données en différents flux à l’aide de la transformation de fractionnement conditionnel dans un flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 9ace415aa725a82d8feda5702d25d7e5ff9875d9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676815"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformation de fractionnement conditionnel dans un flux de données de mappage

La transformation de fractionnement conditionnel route les lignes de données vers différents flux en fonction de conditions de correspondance. La transformation de fractionnement conditionnel est similaire à une structure de décision CASE dans un langage de programmation. La transformation évalue les expressions, et selon les résultats, dirige la ligne de données vers le flux spécifié.

## <a name="configuration"></a>Configuration

Le paramètre **SplitOn** détermine si la ligne de données est acheminée vers le premier flux correspondant ou vers chaque flux auquel elle correspond.

Utilisez le générateur d’expressions de flux de données afin d’entrer une expression pour la condition de fractionnement. Pour ajouter une nouvelle condition, cliquez sur l’icône plus dans une ligne existante. Vous pouvez également ajouter un flux par défaut pour les lignes qui ne correspondent à aucune condition.

![fractionnement conditionnel](media/data-flow/conditionalsplit1.png "options de fractionnement conditionnel")

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Exemples

L’exemple ci-dessous illustre une transformation de fractionnement conditionnel nommée `SplitByYear` traitant le flux entrant `CleanData`. Cette transformation a deux conditions de fractionnement : `year < 1960` et `year > 1980`. `disjoint` a la valeur false, car les données sont acheminées suivant la première condition correspondante. Chaque ligne correspondant à la première condition est acheminée vers le flux de sortie `moviesBefore1960`. Toutes les lignes restantes correspondant à la deuxième condition sont acheminées vers le flux de sortie `moviesAFter1980`. Toutes les autres lignes sont acheminées vers le flux par défaut `AllOtherMovies`.

Dans l’expérience utilisateur Data Factory, cette transformation se présente comme dans l’image ci-dessous :

![fractionnement conditionnel](media/data-flow/conditionalsplit1.png "options de fractionnement conditionnel")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Étapes suivantes

Les transformations de flux de données courantes utilisées avec le fractionnement conditionnel sont la [transformation de jointure](data-flow-join.md), la [transformation de recherche](data-flow-lookup.md) et la [transformation de sélection](data-flow-select.md).
