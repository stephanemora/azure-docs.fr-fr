---
title: Transmettre les informations de déclencheur au pipeline
description: Apprendre à référencer des métadonnées de déclencheur dans un pipeline
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 4d51377daeeb16d7e3e74a77a4379b6fc13cb94b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641561"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Référencer des métadonnées de déclencheur dans des exécutions de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment les métadonnées de déclencheur, telles que l’heure de début du déclencheur, peuvent être utilisées dans l’exécution du pipeline.

Le pipeline doit parfois comprendre et lire les métadonnées du déclencheur qui l’appelle. Par exemple, avec l’exécution du déclencheur de fenêtre bascule, en fonction de l’heure de début et de fin de la fenêtre, le pipeline traite les différents dossiers ou tranches de données. Dans Azure Data Factory, nous utilisons le paramétrage et la [variable système](control-flow-system-variables.md) pour transmettre les métadonnées du déclencheur au pipeline.

Ce modèle est particulièrement utile pour le [déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md), où le déclencheur fournit l’heure de début et de fin de la fenêtre, ainsi que le [déclencheur d’événements personnalisés](how-to-create-custom-event-trigger.md), où le déclencheur analyse et traite les valeurs dans le [champ de _données_ personnalisées](../event-grid/event-schema.md).

> [!NOTE]
> Un type de déclencheur différent fournit des informations de métadonnées différentes. Pour plus d’informations, consultez [Variable système](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>IU de la fabrique de données

Cette section vous montre comment transmettre les informations de métadonnées d’un déclencheur à un pipeline, dans l’interface utilisateur du Azure Data Factory.

1. Accéder au **Canevas de création** et modifier un pipeline

1. Cliquez sur le canevas vide pour afficher les paramètres du pipeline. Ne sélectionnez aucune activité. Vous devrez peut-être extraire le panneau de paramètres du bas du canevas, car il a peut-être été réduit

1. Sélectionnez la section **Paramètres** et sélectionnez **+ Nouveau** pour ajouter des paramètres

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Capture d’écran du paramètre de pipeline montrant comment définir des paramètres dans le pipeline.":::

1. Ajoutez des déclencheurs au pipeline en cliquant sur **+ Déclencheur**.

1. Créez ou joignez un déclencheur au pipeline, puis cliquez sur **OK**

1. Dans la page suivante, renseignez les métadonnées de déclencheur pour chaque paramètre. Utilisez le format défini dans la [Variable système](control-flow-system-variables.md) pour récupérer les informations du déclencheur. Vous n’avez pas besoin de renseigner les informations pour tous les paramètres, mais uniquement ceux qui supposent des valeurs de métadonnées de déclencheur. Par exemple, nous attribuons ici l’heure de début de l’exécution du déclencheur à *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Capture d’écran de la page définition du déclencheur montrant comment transmettre des informations de déclencheur aux paramètres du pipeline.":::

1. Pour utiliser les valeurs dans le pipeline, utilisez les paramètres _@pipeline().parameters.parameterName_, et __pas__ la variable système, dans les définitions de pipeline. Par exemple, dans notre cas, pour lire l’heure de début du déclencheur, nous allons faire référence à @pipeline().parameters.parameter_1.

## <a name="json-schema"></a>Schéma JSON

Pour transmettre les informations de déclencheur aux exécutions de pipeline, le déclencheur et le fichier json du pipeline doivent être mis à jour avec la section _Paramètres_.

### <a name="pipeline-definition"></a>Définition de pipeline

Sous la section **Propriétés**, ajoutez les définitions de paramètres à la section **Paramètres**

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Définition du déclencheur

Sous la section **Pipelines**, attribuez des valeurs de paramètre dans la section **Paramètres**. Vous n’avez pas besoin de renseigner les informations pour tous les paramètres, mais uniquement ceux qui supposent des valeurs de métadonnées de déclencheur.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Utiliser les informations de déclencheur dans le pipeline

Pour utiliser les valeurs dans le pipeline, utilisez les paramètres _@pipeline().parameters.parameterName_, et __pas__ la variable système, dans les définitions de pipeline.

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#trigger-execution).
