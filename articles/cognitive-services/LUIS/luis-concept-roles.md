---
title: Rôles pour les entités - LUIS
titleSuffix: Azure Cognitive Services
description: Les rôles sont des sous-types contextuels nommés d’une entité utilisée uniquement dans des modèles. Par exemple, dans l’énoncé `buy a ticket from New York to London`, New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: eead9e0fec8ac4322bc7816de4a4774f8be8129c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257966"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Rôles d’entité pour les sous-types contextuels

Les rôles permettent à des entités d’avoir des sous-types nommés. Un rôle peut être utilisé avec un type d’entité prédéfini ou personnalisé, à la fois dans les exemple d’énoncés et de modèles. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Exemple de rôles d’une entité après un apprentissage automatique

Dans l’énoncé « acheter un billet de **New York** à **Londres** », New York et Londres sont des villes, mais chacune a une signification différente dans la phrase. New York est la ville d’origine et Londres est la ville de destination. 

```
buy a ticket from New York to London
```

Les rôles donnent un nom à ces différences :

|Type d’entité|Nom de l’entité|Role|Objectif|
|--|--|--|--|
|Simple|Location|origin|où le plan part de|
|Simple|Location|destination|où le plan arrive à|

## <a name="non-machine-learned-entity-example-of-roles"></a>Exemple de rôles d’une entité sans apprentissage automatique

Dans l’énoncé « Planifier la réunion de 8 à 9 », les deux les nombres indiquent une heure, mais chaque heure a une signification différente dans l’énoncé. Les rôles indiquent le nom qui permet de faire la différence. 

```
Schedule the meeting from 8 to 9
```

|Type d’entité|Nom de rôle|Valeur|
|--|--|--|
|datetimeV2 prédéfini|Starttime|8|
|datetimeV2 prédéfini|Endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Plusieurs entités d’un énoncé sont-elles la même chose que des rôles ? 

Plusieurs entités peuvent exister dans un énoncé et être extraites sans utiliser de rôles. Si le contexte de la phrase indique quelle version de l’entité a une valeur, alors un rôle doit être utilisé. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>N’utilisez pas de rôles pour les doublons qui n’ont pas de signification

Si l’énoncé inclut une liste d’emplacements, `I want to travel to Seattle, Cairo, and London.`, il s’agit d’une liste où chaque élément n’a qu’une signification. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Utilisez des rôles si les doublons indiquent un sens

Si l’énoncé inclut une liste d’emplacements ayant une signification, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, cette signification d’origine, de transit et de destination doit être capturée avec des rôles.

### <a name="roles-can-indicate-order"></a>Les rôles peuvent indiquer l’ordre

Si l’énoncé est modifié pour indiquer l’ordre dans lequel vous souhaitez extraire, `I want to first start with Seattle, second London, then third Cairo`, vous pouvez extraire de plusieurs façons. Vous pouvez marquer les jetons qui indiquent le rôle, `first start with`, `second`, `third`. Vous pouvez également utiliser les entités prédéfinies **Ordinal** et **GeographyV2** dans une entité composite pour capturer l’idée d’ordre et d’emplacement. 

## <a name="how-are-roles-used-in-example-utterances"></a>Comment les rôles sont-ils utilisés dans les exemples d’énoncés ?

Lorsqu’une entité a un rôle et qu’elle est marquée dans un exemple d’énoncé, vous pouvez choisir de sélectionner uniquement l’entité, ou bien l’entité et le rôle. 

Les exemples d’énoncés suivants utilisent des entités et des rôles :

|Vue du jeton|Vue de l’entité|
|--|--|
|Je souhaite en savoir plus sur **Seattle**|Je souhaite en savoir plus sur {Location}|
|Acheter un ticket de Seattle à New York|Acheter un ticket de {Location:Origin} à {Location:Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Comment les rôles sont-ils utilisés dans les modèles ?
Dans l’énoncé d’un gabarit de modèle, les rôles sont utilisés dans l’énoncé : 

|Modèle avec des rôles d’entités|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxe du rôle dans les modèles
L’entité et le rôle sont placés entre parenthèses, `{}`. L’entité et le rôle sont séparés par un signe deux-points. 

## <a name="entity-roles-versus-collaborator-roles"></a>Rôles d’entités et rôles de collaborateurs

Les rôles d’entité s’appliquent au modèle de données de l’application LUIS. Les rôles [Collaborateur ou contributeur](luis-concept-keys.md#contributions-from-other-authors) s'appliquent aux niveaux d’accès de création. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utiliser un [tutoriel pratique](tutorial-entity-roles.md) avec des rôles d’entités sans apprentissage automatique
* Découvrir comment ajouter des [rôles](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
