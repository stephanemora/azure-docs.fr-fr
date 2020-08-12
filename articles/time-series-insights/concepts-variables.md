---
title: Variables de modèle – Azure Time Series Insights Gen2 | Microsoft Docs
description: Variables de modèle
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 73d5c3abb2edc940bee9727ce1f3b0c4e8e0a62e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289954"
---
# <a name="time-series-model-variables"></a>Variables de modèle de série chronologique

Cet article décrit les variables de modèles de série chronologique qui spécifient des règles de formule et de calcul sur les événements.

Chaque variable peut être de trois types : *numérique*, *catégoriel* et *agrégé*.

* Les types **numériques** fonctionnent avec des valeurs numériques continues.
* Les types **catégoriels** fonctionnent avec un ensemble défini de valeurs discrètes.
* Les types **agrégés** combinent plusieurs variables d’un même type (toutes numériques ou toutes catégorielles).

Le tableau suivant répertorie les propriétés pertinentes pour chaque genre de variable.

[![Table de variables de modèle de série chronologique](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variables numériques

| Propriétés de la variable | Description |
| --- | ---|
| Variable filter | Les filtres sont des clauses conditionnelles facultatives pour limiter le nombre de lignes à prendre en compte pour le calcul. |
| Valeur de variable | Valeurs de télémétrie utilisées pour le calcul provenant de l’appareil ou des capteurs ou transformées à l’aide d’expressions de série chronologique. Les variables de type numérique doivent être du type *Double*.|
| Interpolation de variables | L’interpolation spécifie comment reconstruire un signal en utilisant des données existantes. Les options d’interpolation *pas à pas* et *linéaire* sont disponibles pour les variables numériques. |
| Variable aggregation | Effectuez des calculs via les [fonctions d’agrégation prises en charge pour les types de variable numériques](https://docs.microsoft.com/rest/api/time-series-insights/preview#numeric-variable-kind-1). |

Les variables sont conformes à l’exemple JSON suivant :

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

#### <a name="categorical-variables"></a>Variables catégorielles

| Propriétés de la variable | Description |
| --- | ---|
| Variable filter | Les filtres sont des clauses conditionnelles facultatives pour limiter le nombre de lignes à prendre en compte pour le calcul. |
| Valeur de variable | Valeurs de télémétrie utilisées pour le calcul provenant de l’appareil ou des capteurs. Les variables de type catégoriel doivent être *Long* ou *String*. |
| Interpolation de variables | L’interpolation spécifie comment reconstruire un signal en utilisant des données existantes. L’option d’interpolation *pas à pas* est disponible pour les variables catégorielles. |
| Catégories de variables | Les catégories créent un mappage entre les valeurs provenant de l’appareil ou des capteurs et une étiquette. |
| Catégorie par défaut de la variable | La catégorie par défaut correspond à toutes les valeurs qui ne sont pas mappées dans la propriété « catégories ». |

Les variables sont conformes à l’exemple JSON suivant :

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long" 
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Variables agrégées

| Propriétés de la variable | Description |
| --- | ---|
| Variable filter | Les filtres sont des clauses conditionnelles facultatives pour limiter le nombre de lignes à prendre en compte pour le calcul. |
| Variable aggregation | Effectuez des calculs via les [fonctions d’agrégation prises en charge pour les types de variable agrégés](https://docs.microsoft.com/rest/api/time-series-insights/preview#aggregate-variable-kind-1). |

Les variables sont conformes à l’exemple JSON suivant :

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Les variables sont stockées dans la définition de type d’un modèle de série chronologique et peuvent être fournies inlined via les API afin de remplacer ou compléter la définition stockée.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [modèles Time Series](./concepts-model-overview.md).

* En savoir plus sur la définition intégrée de variables à l’aide de l’[API de requête](./concepts-query-overview.md).

