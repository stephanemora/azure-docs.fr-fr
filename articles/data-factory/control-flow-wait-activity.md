---
title: Activité d’attente
titleSuffix: Azure Data Factory & Azure Synapse
description: L’activité d’attente dans Azure Data Factory et Synapse Analytics interrompt l’exécution d’un pipeline pour une période spécifiée.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: dfc74e9ada7715f9c954123892a93611df7eca9d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798918"
---
# <a name="execute-wait-activity-in-azure-data-factory-and-synapse-analytics"></a>Exécuter l’activité d’attente dans Azure Data Factory et Synapse Analytics
Quand vous utilisez une activité Wait dans un pipeline, celui-ci attend pendant la période spécifiée avant de poursuivre l’exécution des activités suivantes. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
name | Nom de l’activité `Wait`. | String | Oui
type | Doit être défini sur **Wait**. | String | Oui
waitTimeInSeconds | Nombre de secondes pendant lesquelles le pipeline attend avant de poursuivre le traitement. | Integer | Oui

## <a name="example"></a>Exemple

> [!NOTE]
> Cette section fournit des définitions JSON et des exemples de commandes PowerShell pour exécuter le pipeline. Pour une procédure pas à pas avec des instructions détaillées permettant de créer un pipeline en utilisant des définitions JSON et Azure PowerShell, consultez [Didacticiel : créer une fabrique de données avec Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline avec l’activité Wait
Dans cet exemple, le pipeline a deux activités : **Until** et **Wait**. L’activité Wait est configurée pour attendre pendant une seconde. Le pipeline exécute l’activité Web dans une boucle avec une attente d’une seconde entre chaque exécution. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres activités de flux de contrôle prises en charge : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
