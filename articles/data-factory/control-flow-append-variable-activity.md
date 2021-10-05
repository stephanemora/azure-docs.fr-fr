---
title: Ajouter une activité variable
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser l’activité d’ajout de variable afin d’ajouter la valeur d’une variable de tableau existante définie dans un pipeline Data Factory ou Synapse Analytics.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: 9d2a081535f571b139a5cb15cdb85b37e2c3af97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811710"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>Activité d’ajout de variable dans Azure Data Factory et Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Utilisez l’activité d’ajout de variable pour ajouter une valeur à une variable de tableau existante définie dans un pipeline Data Factory ou Synapse Analytics.

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Obligatoire
-------- | ----------- | --------
name | Nom de l’activité dans le pipeline | Oui
description | Texte décrivant l’activité | non
type | Le type d’activité est AppendVariable | Oui
value | Valeur littérale de chaîne ou d’objet d’expression utilisée pour ajouter à la variable spécifiée | Oui
variableName | Nom de la variable qui va être modifiée par l’activité, la variable doit être de type « Array » | Oui

## <a name="next-steps"></a>Étapes suivantes
Découvrir une activité de flux de contrôle connexe : 

- [Définir une activité variable](control-flow-set-variable-activity.md)
