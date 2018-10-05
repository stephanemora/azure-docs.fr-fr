---
title: Comprendre comment les rôles sont utilisés dans les entités basées sur des modèles
titleSuffix: Azure Cognitive Services
description: Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des modèles. Par exemple, dans l’énoncé « acheter un billet de New York à Londres », New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035196"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Les rôles d’entité dans les modèles sont des sous-types contextuels
Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des [modèles](luis-concept-patterns.md).

Par exemple, dans l’énoncé `buy a ticket from New York to London`, New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination. 

Les rôles donnent un nom à ces différences :

|Entité|Rôle|Objectif|
|--|--|--|
|Lieu|origine|d’où l’avion part|
|Lieu|destination|où l’avion arrive|

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
|rôles|modèles|

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment ajouter des [rôles](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
