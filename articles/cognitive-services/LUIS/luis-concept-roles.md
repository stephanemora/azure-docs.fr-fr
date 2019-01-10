---
title: Rôles pour les entités
titleSuffix: Azure Cognitive Services
description: Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des modèles. Par exemple, dans l’énoncé `buy a ticket from New York to London`, New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: cb155486365ffa1beb4657e2d9cc56fcf143b624
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547743"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Les rôles d’entité dans les modèles sont des sous-types contextuels
Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des [modèles](luis-concept-patterns.md).

Par exemple, dans l’énoncé `buy a ticket from New York to London`, New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination. 

Les rôles donnent un nom à ces différences :

|Entité|Rôle|Objectif|
|--|--|--|
|Lieu|origin|où le plan part de|
|Lieu|destination|où le plan arrive à|
|datetimeV2 prédéfini|to|date de fin|
|datetimeV2 prédéfini|from|date de début|

## <a name="how-are-roles-used-in-patterns"></a>Comment les rôles sont-ils utilisés dans les modèles ?
Dans l’énoncé d’un gabarit de modèle, les rôles sont utilisés dans l’énoncé : 

|Modèle avec des rôles d’entités|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxe du rôle dans les modèles
L’entité et le rôle sont placés entre parenthèses, `{}`. L’entité et le rôle sont séparés par un signe deux-points. 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Exemple de rôle d’entité

Un rôle correspond simplement au positionnement appris en contexte d’une entité dans un énoncé. Son efficacité est optimale quand l’énoncé comprend plusieurs instances de ce type d’entité. L’exemple le plus simple pour tout type d’entité consiste à faire la distinction entre un emplacement d’origine et un emplacement de destination. L’emplacement peut être représenté dans un grand nombre de types d’entité différents. 

Citons un cas d’utilisation où un employé doit être transféré d’un service à un autre et où chaque service est un élément dans une liste. Par exemple :  

`Move [PersonName] from [Department:from] to [Department:to]`. 

Dans la prédiction retournée, les deux entités de service sont retournées dans la réponse JSON et chacune inclut le nom du rôle. 

## <a name="roles-with-prebuilt-entities"></a>Rôles avec des entités prédéfinies

Utilisez des rôles avec des entités prédéfinies pour donner du sens aux différentes instances de l’entité prédéfinie dans un énoncé. 

### <a name="roles-with-datetimev2"></a>Rôles avec l’entité datetimeV2

L’entité prédéfinie datetimeV2 effectue un travail remarquable pour comprendre un grand nombre de dates et d’heures dans des énoncés. Vous pouvez indiquer des dates et des plages de dates différemment de la compréhension par défaut de l’entité prédéfinie. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment ajouter des [rôles](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
