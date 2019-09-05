---
title: Activité de filtrage dans Azure Data Factory | Microsoft Docs
description: L’activité de filtrage permet de filtrer les entrées.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: a7e2e735baa7e40b4170d3397327e90fc1a5d2d5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141672"
---
# <a name="filter-activity-in-azure-data-factory"></a>Activité de filtrage dans Azure Data Factory
Vous pouvez utiliser une activité de filtrage dans un pipeline pour appliquer une expression de filtre à un tableau d’entrée. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité `Filter`. | Chaîne | OUI
Type | Doit être défini sur **filter** | Chaîne | OUI
condition | Condition à utiliser pour filtrer l’entrée. | Expression | OUI
items | Tableau d’entrée sur lequel le filtre sera appliqué. | Expression | OUI

## <a name="example"></a>Exemples

Dans cet exemple, le pipeline a deux activités : **Filter** et **ForEach**. L’activité filter est configurée pour filtrer les éléments dont la valeur est supérieure à 3 dans le tableau d’entrée. Ensuite, l’activité ForEach effectue une itération sur les valeurs filtrées et attend le nombre de secondes spécifié par la valeur actuelle.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
