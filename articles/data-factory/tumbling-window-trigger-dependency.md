---
title: Créer des dépendances envers des déclencheurs de fenêtre bascule dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer une dépendance envers un déclencheur de fenêtre bascule dans Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574387"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Créer une dépendance de déclencheur de fenêtre bascule

Cet article explique étape par étape comment créer une dépendance envers un déclencheur de fenêtre bascule. Pour des informations générales sur les déclencheurs de fenêtre bascule, voir [Guide pratique pour créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md).

Pour créer une chaîne de dépendance et faire en sorte qu’un déclencheur ne soit exécuté qu’après l’exécution d’un autre déclencheur dans la fabrique de données, utilisez cette fonctionnalité avancée de création d’une dépendance envers une fenêtre bascule.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Créer une dépendance dans l’interface utilisateur de Data Factory

Pour créer une dépendance envers un déclencheur, sélectionnez **Déclencher > Avancé > Nouveau**, puis choisissez un déclencheur présentant un décalage et une taille adaptés. Sélectionnez **Terminer** et publiez les modifications de la fabrique de données pour que les dépendances prennent effet.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Propriétés de la dépendance envers une fenêtre bascule

Une dépendance envers un déclencheur de fenêtre bascule présente les propriétés suivantes :

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

Le tableau suivant donne la liste des attributs nécessaires pour définir une dépendance envers une fenêtre bascule.

| **Nom de la propriété** | **Description**  | **Type** | **Obligatoire** |
|---|---|---|---|
| Déclencheur  | Tous les déclencheurs de fenêtre bascule existants s’affichent dans cette liste déroulante. Choisissez le déclencheur sur lequel portera la dépendance.  | TumblingWindowTrigger | Oui |
| Offset | Décalage du déclencheur de dépendance. Indiquez une valeur au format TimeSpan ; sont autorisés les décalages positifs et négatifs. Ce paramètre est obligatoire si le déclencheur dépend de lui-même, facultatif dans tous les autres cas. L’autodépendance doit toujours correspondre à un décalage négatif. | Timespan | Autodépendance : Oui / Autres : Non  |
| Taille de la fenêtre | Taille de la fenêtre bascule de dépendance. Indiquez une valeur au format TimeSpan. Ce paramètre est facultatif. | Timespan | Non   |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Propriétés d’autodépendance d’une fenêtre bascule

Dans les scénarios où le déclencheur ne doit pas passer à la fenêtre suivante tant que la fenêtre précédente n’est pas terminée, créez une autodépendance. Le déclencheur d’autodépendance aura les propriétés ci-dessous :

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

Voici les illustrations des scénarios et de l’utilisation des propriétés de dépendance envers une fenêtre bascule.

## <a name="dependency-offset"></a>Décalage de la dépendance

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Taille de la dépendance

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Autodépendance

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Scénarios d’utilisation

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dépendance envers un autre déclencheur de fenêtre bascule

Par exemple, une tâche de traitement quotidien des données de télémétrie dépend d’une autre tâche quotidienne d’agrégation de la sortie des sept derniers jours et génère des flux de sept jours sur fenêtre glissante :

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Dépendance envers soi-même

Voici une tâche quotidienne sans écart dans les flux de sortie :

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Effectuer le monitoring des dépendances

Vous pouvez effectuer le monitoring de la chaîne de dépendance et des fenêtres correspondantes sur la page de monitoring des exécutions du déclencheur. Accédez à **Monitoring > Exécutions du déclencheur**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Cliquez sur le miroir pour afficher toutes exécutions du déclencheur dépendantes sur la fenêtre sélectionnée.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Étapes suivantes

Voir [Guide pratique pour créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md).