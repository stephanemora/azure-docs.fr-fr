---
title: Transformation de filtre dans un flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Filtrez les lignes à l’aide de la transformation de filtre dans un flux de données de mappage dans Azure Data Factory ou Synapse Analytics.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3cf00a5ea80278973a548a19e9ef0dc82ef877f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060192"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformation de filtre dans un flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Les transformations de filtre permettent de filtrer des lignes en fonction d’une condition. Le flux de sortie comprend toutes les lignes qui correspondent à la condition de filtrage. La transformation de filtre est similaire à une clause WHERE dans SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Configuration

Utilisez le générateur d’expressions de flux de données afin d’entrer une expression pour la condition de filtre. Pour ouvrir le générateur d’expressions, cliquez sur la zone bleue. La condition de filtre doit être de type booléen. Pour plus d’informations sur la création d’une expression, consultez la documentation sur le [générateur d’expressions](concepts-data-flow-expression-builder.md).

:::image type="content" source="media/data-flow/filter1.png" alt-text="Transformation de filtre":::

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exemple

L’exemple ci-dessous illustre une transformation de filtre nommée `FilterBefore1960` traitant le flux entrant `CleanData`. La condition de filtre est l’expression `year <= 1960`.

Dans l’IU, cette transformation se présente comme dans l’image ci-dessous :

:::image type="content" source="media/data-flow/filter1.png" alt-text="Transformation de filtre":::

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Étapes suivantes

Filtrer des colonnes avec la [transformation de sélection](data-flow-select.md)
