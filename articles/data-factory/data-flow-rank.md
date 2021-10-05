---
title: Transformation du classement dans le flux de données de mappage
description: Découvrez comment utiliser une transformation du classement du flux de données de mappage pour générer une colonne de classement dans des pipelines Azure Data Factory ou Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3af0355a02badb5aaa5d5b13a51b74392c931d8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059872"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Transformation du classement dans le flux de données de mappage 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Utilisez la transformation de classement pour générer un classement ordonné en fonction des conditions de tri spécifiées par l’utilisateur. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuration

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="Rank settings":::

**Insensible à la casse :** Si une colonne de tri est de type chaîne, la casse sera factorisée dans le classement. 

**Dense :** Si l’option est activée, la colonne de classement sera classée dans un classement dense. Chaque classement sera un nombre consécutif et les valeurs de classement ne seront pas ignorées en cas d’égalité.

**Colonne de classement :** Nom de la colonne de classement générée. Cette colonne sera de type long.

**Conditions de tri :** Choisissez les colonnes sur lesquelles porte le tri et l’ordre dans lequel il se produit. L’ordre détermine la priorité de tri.

La configuration ci-dessus accepte les données de basket-ball entrantes et crée une colonne de classement appelée « pointsRanking ». La ligne ayant la valeur la plus élevée de la colonne *PTS* aura une valeur *pointsRanking* de 1.

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemple

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="Rank settings":::

Le script de flux de données correspondant à la configuration de classement ci-dessus est présenté dans l’extrait de code suivant.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Étapes suivantes

Filtrez les lignes en fonction des valeurs de classement à l’aide de la [transformation de filtre](data-flow-filter.md).
