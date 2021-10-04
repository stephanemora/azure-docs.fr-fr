---
title: Activité de validation
titleSuffix: Azure Data Factory & Azure Synapse
description: L’activité de validation dans Azure Data Factory et Synapse Analytics retarde l’exécution du pipeline jusqu’à ce qu’un jeu de données soit validé avec des critères définis par l’utilisateur.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: fa98ef27b5dbcc7949f37bf548c414d015224e4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750619"
---
# <a name="validation-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Exécuter l’activité de validation dans les pipelines Azure Data Factory et Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez utiliser une Validation dans un pipeline pour garantir que l’exécution du pipeline se poursuit uniquement une fois qu’elle a confirmé que la référence du jeu de données attaché existe, qu’elle remplit les critères spécifiés ou que le délai d’attente a été atteint.


## <a name="syntax"></a>Syntaxe

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
name | Nom de l’activité « Validation » | String | Oui |
type | Doit être défini sur **Validation**. | String | Oui |
dataset | L’activité bloquera l’exécution jusqu’à ce qu’elle ait confirmé que cette référence de jeu de données existe et qu’elle remplit les critères spécifiés, ou que le délai d’attente ait été atteint. Le jeu de données fourni doit prendre en charge la propriété « MinimumSize » ou « ChildItems ». | Référence de jeu de données | Oui |
délai d'expiration | Spécifie le délai d’expiration d’exécution de l’activité. Si aucune valeur n’est spécifiée, la valeur par défaut est sept jours (« 7:00:00:00 »). Le format est j.hh:mm:ss | String | Non |
sleep | Délai en secondes entre les tentatives de validation. Si aucune valeur n’est spécifiée, la valeur par défaut est 10 secondes. | Integer | Non |
childItems | Vérifie si le dossier a des éléments enfants. Peut être défini sur -true : vérifie que le dossier existe et qu’il a des éléments. Bloque jusqu’à ce qu’au moins un élément se trouve dans le dossier ou que la valeur de délai d’attente soit atteinte. -false : vérifie que le dossier existe et qu’il est vide. Bloque jusqu’à ce que le dossier soit vide ou que la valeur de délai d’attente soit atteinte. Si aucune valeur n’est spécifiée, l’activité bloquera jusqu’à ce que le dossier existe ou que le délai d’attente soit atteint. | Boolean | Non |
minimumSize | Taille minimale d’un fichier en octets. Si aucune valeur n’est spécifiée, la valeur par défaut est de 0 octet | Integer | Non |


## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres activités de flux de contrôle prises en charge :

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
