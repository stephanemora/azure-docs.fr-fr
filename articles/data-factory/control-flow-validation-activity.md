---
title: Activité de validation dans Azure Data Factory | Microsoft Docs
description: L’activité de Validation s’arrête l’exécution du pipeline jusqu'à ce qu’il valide le jeu de données attaché à certains critères Spécifie l’utilisateur.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522948"
---
# <a name="validation-activity-in-azure-data-factory"></a>Activité de validation dans Azure Data Factory
Vous pouvez utiliser une Validation dans un pipeline pour garantir le pipeline continue uniquement l’exécution une fois qu’il a validé le fichier joint référence de jeu de données existe, qu’il répond aux critères spécifiés, ou délai d’attente a été atteint.


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
Nom | Nom de l’activité « Validation » | Chaîne | Oui |
Type | Doit être définie sur **Validation**. | Chaîne | Oui |
dataset | Activité va bloquer l’exécution jusqu'à ce qu’il a validé cette référence de jeu de données existe et qu’il répond aux critères spécifiés, ou délai d’attente a été atteinte. Jeu de données fourni doit prendre en charge la propriété « MinimumSize » ou « ChildItems ». | Référence de DataSet | Oui |
timeout | Spécifie le délai d’expiration d’exécution de l’activité. Si aucune valeur n’est spécifiée, la valeur par défaut est 7 jours (« 7.00:00:00 »). Le format est d.hh:mm:ss | Chaîne | Non  |
veille | Un délai en secondes entre les tentatives de validation. Si aucune valeur n’est spécifiée, la valeur par défaut est 10 secondes. | Entier  | Non  |
childItems | Vérifie si le dossier a des éléments enfants. Peut être définie à true : Valider que le dossier existe et qu’il possède des éléments. Bloque jusqu'à ce qu’au moins un élément se trouve dans le dossier ou la valeur de délai d’attente est atteinte.-false : Vérifiez que le dossier existe et qu’il est vide. Bloque jusqu'à ce que le dossier est vide ou jusqu'à ce que le délai d’expiration la valeur est atteinte. Si aucune valeur n’est spécifiée, l’activité bloquera jusqu'à ce que le dossier existe ou jusqu'à ce que le délai d’expiration est atteint. | Booléen | Non  |
minimumSize | Taille minimale d’un fichier en octets. Si aucune valeur n’est spécifiée, valeur par défaut est de 0 octet | Entier  | Non  |


## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory :

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
