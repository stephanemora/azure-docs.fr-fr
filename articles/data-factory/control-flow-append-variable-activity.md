---
title: Activité d’ajout de variable dans Azure Data Factory
description: Découvrez comment utiliser l’activité d’ajout de variable afin d’ajouter la valeur d’une variable de tableau existante définie dans un pipeline Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783844"
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
