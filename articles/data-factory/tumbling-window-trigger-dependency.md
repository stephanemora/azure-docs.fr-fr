---
title: Créer des dépendances de déclencheur de fenêtre bascule
description: Découvrez comment créer une dépendance envers un déclencheur de fenêtre bascule dans Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 3a4d31cb6986f8fc841a6afe20388e40e9f28c9b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926673"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Créer une dépendance de déclencheur de fenêtre bascule

Cet article explique étape par étape comment créer une dépendance envers un déclencheur de fenêtre bascule. Pour des informations générales sur les déclencheurs de fenêtre bascule, voir [Guide pratique pour créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md).

Pour créer une chaîne de dépendance et faire en sorte qu’un déclencheur ne soit exécuté qu’après l’exécution d’un autre déclencheur dans la fabrique de données, utilisez cette fonctionnalité avancée de création d’une dépendance envers une fenêtre bascule.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Créer une dépendance dans l’interface utilisateur de Data Factory

Pour créer une dépendance envers un déclencheur, sélectionnez **Déclencher > Avancé > Nouveau**, puis choisissez un déclencheur présentant un décalage et une taille adaptés. Sélectionnez **Terminer** et publiez les modifications de la fabrique de données pour que les dépendances prennent effet.

![Création de dépendances](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Création de dépendances")

## <a name="tumbling-window-dependency-properties"></a>Propriétés de la dépendance envers une fenêtre bascule

Un déclencheur de fenêtre bascule avec une dépendance présente les propriétés suivantes :

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Le tableau suivant donne la liste des attributs nécessaires pour définir une dépendance envers une fenêtre bascule.

| **Nom de la propriété** | **Description**  | **Type** | **Obligatoire** |
|---|---|---|---|
| Type  | Tous les déclencheurs de fenêtre bascule existants s’affichent dans cette liste déroulante. Choisissez le déclencheur sur lequel portera la dépendance.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | OUI |
| offset | Décalage du déclencheur de dépendance. Indiquez une valeur au format TimeSpan ; sont autorisés les décalages positifs et négatifs. Cette propriété est obligatoire si le déclencheur dépend de lui-même, facultative dans tous les autres cas. L’autodépendance doit toujours correspondre à un décalage négatif. Si aucune valeur n’est spécifiée, la fenêtre est la même que le déclencheur lui-même. | Timespan<br/>(hh:mm:ss) | Autodépendance : OUI<br/>Autre : Non |
| size | Taille de la fenêtre bascule de dépendance. Fournissez une valeur TimeSpan positive. Cette propriété est facultative. | Timespan<br/>(hh:mm:ss) | Non  |

> [!NOTE]
> Un déclencheur de fenêtre bascule peut dépendre d’un maximum de deux autres déclencheurs.

## <a name="tumbling-window-self-dependency-properties"></a>Propriétés d’autodépendance d’une fenêtre bascule

Dans les scénarios où le déclencheur ne doit pas passer à la fenêtre suivante tant que la fenêtre précédente n’est pas terminée, créez une autodépendance. Un déclencheur d'autodépendance dépend de la réussite de ses exécutions antérieures, au cours de l’heure précédente, et présente les propriétés ci-dessous :

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
## <a name="usage-scenarios-and-examples"></a>Scénarios et exemples d’utilisation

Voici les illustrations des scénarios et de l’utilisation des propriétés de dépendance envers une fenêtre bascule.

### <a name="dependency-offset"></a>Décalage de la dépendance

![Exemple de décalage](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Exemple de décalage")

### <a name="dependency-size"></a>Taille de la dépendance

![Exemple de taille](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Exemple de taille")

### <a name="self-dependency"></a>Autodépendance

![Autodépendance](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Autodépendance")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dépendance envers un autre déclencheur de fenêtre bascule

Une tâche de traitement quotidien des données de télémétrie dépend d’une autre tâche quotidienne d’agrégation de la sortie des sept derniers jours et génère des flux de sept jours sur fenêtre glissante :

![Exemple de dépendance](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Exemple de dépendance")

### <a name="dependency-on-itself"></a>Dépendance envers soi-même

Voici une tâche quotidienne sans écart dans les flux de sortie :

![Exemple d’autodépendance](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exemple d’autodépendance")

## <a name="monitor-dependencies"></a>Effectuer le monitoring des dépendances

Vous pouvez effectuer le monitoring de la chaîne de dépendance et des fenêtres correspondantes sur la page de monitoring des exécutions du déclencheur. Accédez à **Monitoring > Exécutions du déclencheur**. Sous la colonne Actions, vous pouvez réexécuter le déclencheur ou afficher ses dépendances.

![Superviser les exécutions de déclencheur](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Surveiller les exécutions du déclencheur")

Si vous cliquez sur « Afficher les dépendances du déclencheur », vous pouvez voir l’état des dépendances. Si un des déclencheurs de dépendance échoue, vous devez le réexécuter correctement pour que le déclencheur dépendant s’exécute. Un déclencheur de fenêtre bascule attend des dépendances pendant sept jours avant d’arriver à expiration.

![Superviser les dépendances](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Effectuer le monitoring des dépendances")

Pour un rendu plus visuel de la planification des dépendances du déclencheur, sélectionnez l’affichage de Gantt.

![Superviser les dépendances](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Effectuer le monitoring des dépendances")

## <a name="next-steps"></a>Étapes suivantes

* Consulter le [Guide pratique pour créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md)
