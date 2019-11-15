---
title: Transformation de filtre dans un flux de données de mappage Azure Data Factory
description: Filtrer des lignes à l’aide de la transformation de filtre dans un flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676171"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformation de filtre dans un flux de données de mappage

Les transformations de filtre permettent de filtrer des lignes en fonction d’une condition. Le flux de sortie comprend toutes les lignes qui correspondent à la condition de filtrage. La transformation de filtre est similaire à une clause WHERE dans SQL.

## <a name="configuration"></a>Configuration

Utilisez le générateur d’expressions de flux de données afin d’entrer une expression pour la condition de filtre. Pour ouvrir le générateur d’expressions, cliquez sur la zone bleue. La condition de filtre doit être de type booléen. Pour plus d’informations sur la création d’une expression, consultez la documentation sur le [générateur d’expressions](concepts-data-flow-expression-builder.md).

![Transformation de filtre](media/data-flow/filter1.png "Transformation de filtre")

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exemples

L’exemple ci-dessous illustre une transformation de fractionnement conditionnel nommée `FilterBefore1960` traitant le flux entrant `CleanData`. La condition de filtre est l’expression `year <= 1960`.

Dans l’expérience utilisateur Data Factory, cette transformation se présente comme dans l’image ci-dessous :

![Transformation de filtre](media/data-flow/filter1.png "Transformation de filtre")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Étapes suivantes

Filtrer des colonnes avec la [transformation de sélection](data-flow-select.md)
