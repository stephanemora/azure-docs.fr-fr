---
title: Activité de définition de variable dans Azure Data Factory
description: Découvrez comment utiliser l’activité de définition de variable afin de définir la valeur d’une variable existante définie dans un pipeline Data Factory
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786156"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Activité de définition de variable dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilisez l’activité de définition de variable pour définir la valeur d’une variable existante de type Chaîne, Booléen ou Tableau définie dans un pipeline Data Factory.

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Obligatoire
-------- | ----------- | --------
name | Nom de l’activité dans le pipeline | Oui
description | Texte décrivant l’activité | non
type | Doit être défini sur **SetVariable** | Oui
value | Chaîne littérale ou valeur d’objet d’expression à laquelle la variable est affectée | Oui
variableName | Nom de la variable définie par cette activité | Oui

## <a name="incrementing-a-variable"></a>Incrémentation d’une variable

Un scénario courant impliquant les variables dans Azure Data Factory consiste à utiliser une variable comme itérateur dans une activité until ou foreach. Dans une activité de définition de variable, vous ne pouvez pas référencer la variable définie dans le champ `value`. Pour contourner cette limitation, définissez une variable temporaire, puis créez une deuxième activité de définition de variable. La deuxième activité de définition de variable définit la valeur de l’itérateur sur la variable temporaire. 

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


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur une activité de flux de contrôle prise en charge par Data Factory : 

- [Activité d’ajout de variable](control-flow-append-variable-activity.md)
