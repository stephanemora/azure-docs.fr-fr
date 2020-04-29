---
title: Activité d’ajout de variable dans Azure Data Factory
description: Découvrez comment utiliser l’activité d’ajout de variable afin d’ajouter la valeur d’une variable de tableau existante définie dans un pipeline Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414213"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Activité d’ajout de variable dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Utilisez l’activité d’ajout de variable afin d’ajouter la valeur d’une variable de tableau existante définie dans un pipeline Data Factory.

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Obligatoire
-------- | ----------- | --------
name | Nom de l’activité dans le pipeline | Oui
description | Texte décrivant l’activité | non
type | Le type d’activité est AppendVariable | Oui
value | Valeur littérale de chaîne ou d’objet d’expression utilisée pour ajouter à la variable spécifiée | Oui
variableName | Nom de la variable qui va être modifiée par l’activité, la variable doit être de type « Array » | Oui

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur une activité de flux de contrôle prise en charge par Data Factory : 

- [Définir une activité variable](control-flow-set-variable-activity.md)
