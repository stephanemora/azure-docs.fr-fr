---
title: Comprendre comment les rôles sont utilisés dans les entités basées sur des modèles
titleSuffix: Azure Cognitive Services
description: Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des modèles. Par exemple, dans l’énoncé « acheter un billet de New York à Londres », New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9bbbb797cd7e7d1cea52f1d5b1b491998b595db7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638073"
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

## <a name="roles-versus-hierarchical-entities"></a>Rôles et entités hiérarchiques
Les entités hiérarchiques fournissent les mêmes informations contextuelles que les rôles, mais uniquement pour les énoncés dans des **intentions**. De même, les rôles fournissent les mêmes informations contextuelles que les entités hiérarchiques, mais uniquement dans des **modèles**.

|Apprentissage contextuel|Utilisé dans|
|--|--|
|entités hiérarchiques|intentions|
|roles|modèles|

## <a name="roles-with-prebuilt-entities"></a>Rôles avec des entités prédéfinies

Utilisez des rôles avec des entités prédéfinies pour donner du sens aux différentes instances de l’entité prédéfinie dans un énoncé. 

### <a name="roles-with-datetimev2"></a>Rôles avec l’entité datetimeV2

L’entité prédéfinie datetimeV2 effectue un travail remarquable pour comprendre un grand nombre de dates et d’heures dans des énoncés. Vous pouvez indiquer des dates et des plages de dates différemment de la compréhension par défaut de l’entité prédéfinie. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment ajouter des [rôles](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
