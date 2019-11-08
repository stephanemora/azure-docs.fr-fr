---
title: Activité de définition de variable dans Azure Data Factory
description: Découvrez comment utiliser l’activité de définition de variable afin de définir la valeur d’une variable existante définie dans un pipeline Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 2589e56d3170b235f31d07f902eb75c749e8573f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679298"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Activité de définition de variable dans Azure Data Factory

Utilisez l’activité de définition de variable pour définir la valeur d’une variable existante de type Chaîne, Booléen ou Tableau définie dans un pipeline Data Factory.

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Obligatoire
-------- | ----------- | --------
Nom | Nom de l’activité dans le pipeline | OUI
description | Texte décrivant l’activité | no
Type | Le type d’activité est SetVariable | Oui
value | Valeur littérale de chaîne ou d’objet d’expression utilisée pour définir la variable spécifiée | Oui
variableName | Nom de la variable qui sera définie par cette activité | Oui


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur une activité de flux de contrôle prise en charge par Data Factory : 

- [Activité d’ajout de variable](control-flow-append-variable-activity.md)
