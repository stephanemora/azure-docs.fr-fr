---
title: Rôles pour les entités
titleSuffix: Azure Cognitive Services
description: Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des modèles. Par exemple, dans l’énoncé `buy a ticket from New York to London`, New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 318e71b68bbabeeef34c75a412f9fdd5b6db754a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073024"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Rôles d’entité pour les sous-types contextuelles

Les rôles permettent d’entités à avoir nommé sous-types. Un rôle peut être utilisé avec un type d’entité prédéfini ou personnalisé, à la fois dans les exemple d’énoncés et de modèles. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Exemple de machine a appris une entité de rôles

Dans l’énoncé « acheter un ticket à partir **New York** à **Londres**, New York et Londres sont villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination. 

```
buy a ticket from New York to London
```

Les rôles donnent un nom à ces différences :

|Type d'entité|Nom de l’entité|Rôle|Objectif|
|--|--|--|--|
|Simple|Lieu|origine|où le plan part de|
|Simple|Lieu|destination|où le plan arrive à|

## <a name="non-machine-learned-entity-example-of-roles"></a>Exemple d’entité non-machine-a appris de rôles

Dans l’énoncé « Planifier la réunion de 8 à 9 », les deux les nombres indiquent une heure, mais chaque fois a une signification différente dans l’énoncé. Rôles de fournissent le nom des différences. 

```
Schedule the meeting from 8 to 9
```

|Type d'entité|Nom de rôle|Valeur|
|--|--|--|
|datetimeV2 prédéfini|Starttime|8|
|datetimeV2 prédéfini|heure de fin|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Sont plusieurs entités dans un énoncé de la même chose en tant que rôles ? 

Plusieurs entités peuvent exister dans un énoncé et peuvent être extraites sans l’aide de rôles. Si le contexte de la phrase indique à la version de l’entité a une valeur, alors un rôle doit être utilisé. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>N’utilisez pas les rôles pour les doublons sans signification

Si l’énoncé inclut une liste d’emplacements, `I want to travel to Seattle, Cairo, and London.`, il s’agit d’une liste dans lequel chaque élément n’a une signification supplémentaire. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Utiliser des rôles si les doublons indiquent le sens

Si l’énoncé inclut une liste des emplacements ayant une signification, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, cette signification d’origine, d’escale et de destination doit être capturée avec les rôles.

### <a name="roles-can-indicate-order"></a>Rôles peuvent indiquer l’ordre

Si l’énoncé modifié pour indiquer l’ordre dans lequel vous souhaitez extraire, `I want to first start with Seattle, second London, then third Cairo`, vous pouvez extraire de deux façons. Vous pouvez marquer les jetons qui indiquent le rôle, `first start with`, `second`, `third`. Vous pouvez également utiliser l’entité prédéfinie **Ordinal** et **GeographyV2** prédéfinis d’entité dans une entité composite pour capturer l’idée de commande et de la place. 

## <a name="how-are-roles-used-in-example-utterances"></a>Comment les rôles sont utilisés dans les énoncés exemple ?

Lorsqu’une entité a un rôle, et l’entité est marquée dans un énoncé de l’exemple, vous avez le choix de sélectionner uniquement l’entité ou l’entité et le rôle. 

Énoncés d’exemple suivant utilisent les entités et les rôles :

|Vue du jeton|Vue de l’entité|
|--|--|
|Je souhaite en savoir plus sur **Seattle**|Je souhaite en savoir plus sur {Location}|
|Acheter un ticket de Seattle à New York|Acheter un ticket à partir de {emplacement : origine} à {emplacement : Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Comment les rôles sont-ils utilisés dans les modèles ?
Dans l’énoncé d’un gabarit de modèle, les rôles sont utilisés dans l’énoncé : 

|Modèle avec des rôles d’entités|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxe du rôle dans les modèles
L’entité et le rôle sont placés entre parenthèses, `{}`. L’entité et le rôle sont séparés par un signe deux-points. 

## <a name="entity-roles-versus-collaborator-roles"></a>Rôles d’entité par rapport aux rôles de collaborateur

Rôles de l’entité s’appliquent au modèle de données de l’application LUIS. [Collaborateur](luis-concept-collaborator.md) rôles s’appliquent aux niveaux d’accès de création. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utilisez un [didacticiel](tutorial-entity-roles.md) à l’aide de rôles de l’entité avec les entités non-machine-a appris
* Découvrir comment ajouter des [rôles](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
