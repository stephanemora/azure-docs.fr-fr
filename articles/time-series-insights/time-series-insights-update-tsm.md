---
title: Modèle de série chronologique dans Azure Time Series Insights en préversion | Microsoft Docs
description: Comprendre le modèle de série chronologique d’Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6126a9d56059435be32299bcd1f03050f031f81b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507815"
---
# <a name="time-series-model"></a>Modèle de série chronologique

Cet article décrit la partie consacrée au modèle de série chronologique de la préversion d’Azure Time Series Insights. Il décrit le modèle lui-même, ses fonctionnalités, et comment lancer le développement et la mise à jour de votre propre modèle.

En règle générale, les données collectées à partir d’appareils IoT manquent d’informations contextuelles, ce qui empêche de rechercher et d’analyser rapidement les capteurs. Le principal objectif du modèle de série chronologique est de simplifier la recherche et l’analyse des données IoT. Vous pouvez atteindre cet objectif grâce à la collecte, la maintenance et l’enrichissement des données d’une série chronologique afin d’obtenir des jeux de données prêts à l’emploi. 

Les modèles de série chronologique jouent un rôle essentiel dans les requêtes et la navigation en contextualisant les entités destinées ou non à des appareils. Les données persistantes d’un modèle de série chronologique alimentent les calculs de la requête de série chronologique en tirant parti des formules qui y sont stockées.

![tsm][1]

## <a name="key-capabilities"></a>Fonctionnalités clés

Pour faciliter la gestion de la contextualisation, la préversion d’Azure Time Series Insights propose un modèle de série chronologique doté des fonctionnalités suivantes. Vous pouvez ainsi :

* créer et gérer des calculs ou des formules, transformer des données en tirant parti des fonctions scalaires, agréger des opérations, etc. ;

* définir les relations parents-enfants pour permettre la navigation et la référence afin de fournir un contexte à la télémétrie des séries chronologiques ;

* définir les propriétés associées à la partie des instances de *champs d’instance*, et s’en servir pour créer des hiérarchies.

## <a name="times-series-model-key-components"></a>Principaux composants du modèle de série chronologique

Un modèle de série chronologique comprend trois composants principaux :

* Les *types* de modèles de série chronologique
* Les *hiérarchies* de modèle de série chronologique
* Les *instances* de modèle de série chronologique

## <a name="time-series-model-types"></a>Types de modèles de série chronologique

Les *types* de modèles de série chronologique vous aident à définir des variables ou des formules permettant d’effectuer des calculs. Ces types sont associés à une instance Time Series Insights spécifique. Un type peut avoir une ou plusieurs variables. Par exemple, une instance Time Series Insights peut être de type *Capteur de température*, qui se compose des variables *avg temperature* (température moyenne), *min temperature* (température minimale) et *max temperature* (température maximale). Nous créons un type par défaut lorsque les données commencent à affluer vers Time Series Insights. Le type par défaut peut être récupéré et mis à jour à partir des paramètres du modèle. Les types par défaut comportent une variable qui compte le nombre d’événements.

## <a name="time-series-model-type-json-example"></a>Exemple JSON d’un type de modèle de série chronologique

Exemple :

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Pour plus d’informations sur les types de modèles de série chronologique, consultez la [documentation de référence](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>variables

Les types Time Series Insights contiennent des variables qui représentent des calculs nommés selon les valeurs provenant des événements. Les définitions des variables Time Series Insights contiennent des règles de calcul et de formule. Ces définitions de variables incluent *kind*, *value*, *filter*, *reduction* et *boundaries*. Les variables sont stockées dans la définition de type du modèle de série chronologique et peuvent être fournies en ligne via les API de requête afin de remplacer la définition stockée.

La matrice suivante sert de légende pour les définitions de variables :

![table][2]

### <a name="variable-kind"></a>Variable kind

Les types de variables kind suivants sont pris en charge :

* *Numérique*
* *Agrégat*

### <a name="variable-filter"></a>Variable filter

Les variables filter spécifient une clause de filtre facultative pour restreindre le nombre de lignes utilisées pour un calcul en fonction des conditions.

### <a name="variable-value"></a>Variable value

Les variables value doivent être utilisées dans le calcul. Il s’agit de la colonne à laquelle nous devons faire référence dans les événements.

### <a name="variable-aggregation"></a>Variable aggregation

Fonction d’agrégation de la variable qui gère une partie du calcul. Time Series Insights prend en charge les agrégats standard (à savoir, *min*, *max*, *avg*, *sum* et *count*).

## <a name="time-series-model-hierarchies"></a>Hiérarchies de modèle de série de temps

Les hiérarchies classent les instances en spécifiant les noms de propriétés et leurs relations. Vous pouvez avoir une ou plusieurs hiérarchies. Elles n’ont pas besoin de faire partie de vos données actuelles, mais chaque instance doit être mappée à une hiérarchie. Une instance de modèle de série chronologique peut être mappée à une ou plusieurs hiérarchies.

Les hiérarchies sont définies par *ID de hiérarchie*, *nom* et *source*. Elles ont un chemin d’accès qui se présente sous la forme d’un ordre descendant parent-enfant de la hiérarchie que les utilisateurs souhaitent créer. Les propriétés parent-enfant sont mappées aux *champs d’instance*.

### <a name="time-series-model-hierarchy-json-example"></a>Exemple JSON d’une hiérarchie de modèle de série chronologique

Exemple :

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Pour plus d’informations sur les hiérarchies de modèle de série chronologique, consultez la [documentation de référence](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Comportement de définition de hiérarchie

Prenons l’exemple de la hiérarchie H1 suivante, dont la définition contient les attributs *building*, *floor* et *room* :

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Selon les *champs d’instance*, les attributs et les valeurs de la hiérarchie s’affichent comme indiqué dans le tableau suivant :

| ID de série chronologique | Champs d'instance |
| --- | --- |
| ID1 | “building” = “1000”, “floor” = “10”, “room” = “55”  |
| ID2 | “building” = “1000”, “room” = “55” |
| ID3 | “floor” = “10” |
| ID4 | “building” = “1000”, “floor” = “10”  |
| ID5 | Aucune des valeurs “building”, “floor” ou “room” n’est définie |

Dans l’exemple précédent, ID1 et ID4 font partie de la hiérarchie H1 dans l’explorateur Azure Time Series Insights, et les autres attributs sont classés sous *Instances non apparentées* car ils ne correspondent pas à la hiérarchie de données spécifiée.

## <a name="time-series-model-instances"></a>Instances de modèle de série chronologique

Les instances représentent la série chronologique elle-même. Dans la plupart des cas, *deviceId* ou *assetId* est l’identificateur unique de la ressource dans l’environnement. Les instances contiennent des informations descriptives appelées propriétés d’instance. Au minimum, les propriétés d’instance incluent les informations de la hiérarchie. Elles peuvent également inclure des données utiles et des descriptifs, par exemple le fabricant, l’opérateur ou la date du dernier entretien.

Les instances sont définies par *typeId*, *timeSeriesId*, *name*, *description*, *hierarchyIds* et *instanceFields*. Chaque instance est mappée à un seul *type* et à une ou plusieurs hiérarchies. Les instances héritent toutes leurs propriétés de hiérarchies, et d’autres attributs *instanceFields* peuvent être ajoutés pour affiner la définition d’une propriété d’instance.

*instanceFields* représentent des propriétés d’une instance et toutes les données statiques qui définissent une instance. Ils définissent les valeurs des propriétés d’une hiérarchie ou non et prennent en charge l’indexation pour effectuer des opérations de recherche.

La propriété *name* est facultative et sensible à la casse. Si *name* n’est pas disponible, la valeur par défaut est l’ID de série chronologique. Si *name* est fourni, l’ID de la série chronologique est toujours disponible dans le puits (grille sous les graphiques dans l’explorateur). 

## <a name="time-series-model-instance-json-example"></a>Exemple JSON d’une instance de modèle de série chronologique

Exemple :

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Pour plus d’informations sur les instances de modèle de série chronologique, consultez la [documentation de référence](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-settings-example"></a>Exemples de paramètres de modèle de série chronologique

Exemple :

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Pour plus d’informations sur les paramètres de modèle de série chronologique, consultez la [documentation de référence](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Étapes suivantes

Voir [Azure Time Series Insights (préversion) - Stockage et entrée](./time-series-insights-update-storage-ingress.md).

Voir le nouveau [modèle de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
