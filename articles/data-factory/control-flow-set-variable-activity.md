---
title: Définir une activité variable
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser l’activité de définition de variable afin de définir la valeur d’une variable existante définie dans un pipeline Azure Data Factory ou Azure Synapse Analytics.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: c9c489ab15f7b6a44e4aadeef8cd98b0d935e1ba
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821643"
---
# <a name="set-variable-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Activité de définition de variable dans Azure Data Factory et Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilisez l’activité de définition de variable pour définir la valeur d’une variable existante de type Chaîne, Booléen ou Tableau définie dans un pipeline Data Factory ou Synapse.

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Obligatoire
-------- | ----------- | --------
name | Nom de l’activité dans le pipeline | Oui
description | Texte décrivant l’activité | non
type | Doit être défini sur **SetVariable** | Oui
value | Chaîne littérale ou valeur d’objet d’expression à laquelle la variable est affectée | Oui
variableName | Nom de la variable définie par cette activité | Oui

## <a name="incrementing-a-variable"></a>Incrémentation d’une variable

Un scénario courant impliquant des variables consiste à utiliser une variable comme itérateur dans une activité until ou foreach. Dans une activité de définition de variable, vous ne pouvez pas référencer la variable définie dans le champ `value`. Pour contourner cette limitation, définissez une variable temporaire, puis créez une deuxième activité de définition de variable. La deuxième activité de définition de variable définit la valeur de l’itérateur sur la variable temporaire. 

Voici un exemple de ce modèle :

![Incrémenter une variable](media/control-flow-set-variable-activity/increment-variable.png "Incrémenter une variable")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```

Les variables sont actuellement délimitées au niveau du pipeline. Cela signifie qu’elles ne sont pas thread-safe et peuvent entraîner un comportement inattendu et indésirable si elles sont accessibles à partir d’une activité d’itération parallèle comme une boucle foreach, en particulier lorsque la valeur est également modifiée au sein de cette activité foreach.

## <a name="next-steps"></a>Étapes suivantes
Découvrir une autre activité de flux de contrôle connexe : 

- [Activité d’ajout de variable](control-flow-append-variable-activity.md)
