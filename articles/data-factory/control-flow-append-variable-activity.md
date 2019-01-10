---
title: Activité d’ajout de variable dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité d’ajout de variable afin d’ajouter la valeur d’une variable de tableau existante définie dans un pipeline Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: e904075908fe7108c0566856b25fe03be0b7fd86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023793"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Activité d’ajout de variable dans Azure Data Factory

Utilisez l’activité d’ajout de variable afin d’ajouter la valeur d’une variable de tableau existante définie dans un pipeline Data Factory.

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Obligatoire
-------- | ----------- | --------
Nom | Nom de l’activité dans le pipeline | Oui
description | Texte décrivant l’activité | no
Type | Le type d’activité est AppendVariable | Oui
value | Valeur littérale de chaîne ou d’objet d’expression utilisée pour ajouter à la variable spécifiée | Oui
variableName | Nom de la variable qui va être modifiée par l’activité, la variable doit être de type « Array » | Oui

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur une activité de flux de contrôle prise en charge par Data Factory : 

- [Définir une activité variable](control-flow-set-variable-activity.md)
