---
title: Comprendre comment les rôles sont utilisés dans les entités basées sur un modèle - Azure | Microsoft Docs
description: Découvrez comment un rôle est utilisé dans une entité basée sur un modèle pour donner un nom à un sous-type d’entité contextuel.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: d2692cdce9da7428bd7b30c4feaf7347792618f5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222701"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Les rôles d’entité dans les modèles sont des sous-types contextuels
Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des [modèles](luis-concept-patterns.md).

Par exemple, dans l’énoncé `buy a ticket from New York to London`, New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination. 

Les rôles donnent un nom à ces différences :

|Entité|Rôle|Objectif|
|--|--|--|
|Lieu|origin|où le plan part de|
|Lieu|destination|où le plan arrive à|

## <a name="how-are-roles-used-in-patterns"></a>Comment les rôles sont-ils utilisés dans les modèles ?
Dans l’énoncé d’un gabarit de modèle, les rôles sont utilisés dans l’énoncé : 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>Syntaxe du rôle dans les modèles
L’entité et le rôle sont placés entre parenthèses, `{}`. L’entité et le rôle sont séparés par un signe deux-points. 

## <a name="roles-versus-hierarchical-entities"></a>Rôles et entités hiérarchiques
Les entités hiérarchiques fournissent les mêmes informations contextuelles que les rôles, mais uniquement pour les énoncés dans des **intentions**. De même, les rôles fournissent les mêmes informations contextuelles que les entités hiérarchiques, mais uniquement dans des **modèles**.

|Apprentissage contextuel|Utilisé dans|
|--|--|
|entités hiérarchiques|intentions|
|roles|modèles|

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment ajouter des [rôles](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
