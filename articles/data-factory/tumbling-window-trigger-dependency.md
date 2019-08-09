---
title: Créer des dépendances envers des déclencheurs de fenêtre bascule dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer une dépendance envers un déclencheur de fenêtre bascule dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: bf8534ce40460637141bea2b9582e63a2a5cd04a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620633"
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

![Exemple d'autodépendance](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exemple d'autodépendance")

## <a name="monitor-dependencies"></a>Effectuer le monitoring des dépendances

Vous pouvez effectuer le monitoring de la chaîne de dépendance et des fenêtres correspondantes sur la page de monitoring des exécutions du déclencheur. Accédez à **Monitoring > Exécutions du déclencheur**.

![Surveiller les exécutions du déclencheur](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Exemple d'autodépendance")

Cliquez sur l'icône d'action pour afficher toutes les exécutions du déclencheur dépendantes sur la fenêtre sélectionnée.

![Surveiller les dépendances](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Surveiller les dépendances")

Dans l’exemple ci-dessus, un déclencheur quotidien dépend d’un déclencheur horaire sans fenêtre définie et un décalage de 3 heures. Par conséquent, le déclencheur s’exécute après 24 exécutions réussies de la dépendance.

## <a name="next-steps"></a>Étapes suivantes

Voir [Guide pratique pour créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md).