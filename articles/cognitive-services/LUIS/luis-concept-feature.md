---
title: Fonctionnalités – LUIS
titleSuffix: Azure Cognitive Services
description: Ajoutez des fonctionnalités à un modèle de langage afin de fournir des conseils sur la façon de reconnaître les entrées que vous souhaitez étiqueter ou classer.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531896"
---
# <a name="machine-learned-features"></a>Fonctionnalités issues de l’apprentissage automatique

En apprentissage automatique, une _caractéristique_ est un trait ou un attribut distinctif des données que votre système observe et dont il apprend. Dans Language Understanding (LUIS), une caractéristique décrit et explique ce qui est important au sujet de vos intentions et entités.

Dans le [portail LUIS en préversion](https://preview.luis.ai), les caractéristiques sont des _descripteurs_, car elles sont utilisées pour _décrire_ l’intention ou l’entité.

## <a name="features-_descriptors_-in-language-understanding"></a>Caractéristiques (_descripteurs_) dans Language Understanding

Les caractéristiques, également appelées descripteurs, désignent des indices qui aident Language Understanding à identifier les exemples d’énoncés. Voici quelques fonctionnalités :

* Liste d’expressions en tant que caractéristique d’intentions ou d’entités
* Entités en tant que caractéristiques d’intentions ou d’entités

Les caractéristiques doivent être considérées comme une partie nécessaire de votre schéma pour la décomposition du modèle.

## <a name="what-is-a-phrase-list"></a>Définition d’une liste d’expressions

Une liste d’expressions est une liste de mots, d’expressions, de chiffres ou d’autres caractères qui permettent d’identifier le concept que vous essayez d’identifier. Cette liste n’est pas sensible à la casse.

## <a name="when-to-use-a-phrase-list"></a>Quand utiliser une liste d’expressions

À l’aide d’une liste d’expressions, LUIS prend en compte le contexte et généralise pour identifier les éléments qui y sont similaires, sans être une correspondance de texte exacte. Si votre application LUIS doit être en mesure de généraliser et d’identifier de nouveaux éléments, utilisez une liste d’expressions.

Si vous souhaitez pouvoir reconnaître de nouvelles instances, comme un planificateur de réunions qui doit reconnaître les noms de nouveaux contacts ou une application d’inventaire qui doit reconnaître de nouveaux produits, commencez par une entité issue de l’apprentissage automatique. Créez ensuite une liste d’expressions qui aide LUIS à trouver des mots ayant une signification similaire. Cette liste d’expressions guide LUIS afin qu’il reconnaisse des exemples en ajoutant une précision supplémentaire à la valeur de ces mots.

Les listes d’expression sont comme du vocabulaire spécifique à un domaine qui permet d’améliorer la qualité de compréhension des intentions et des entités.

## <a name="considerations-when-using-a-phrase-list"></a>Considérations relatives à l’utilisation d’une liste d’expressions

Par défaut, une liste d’expressions est appliquée à tous les modèles de l’application. Cela fonctionne pour les listes d’expressions qui peuvent recouper toutes les intentions et entités. Pour permettre la décomposition, vous devez appliquer une liste d’expressions uniquement aux modèles auxquels elle s’applique.

Si vous créez une liste d’expressions (créée globalement par défaut), puis que vous l’appliquez ultérieurement comme descripteur (caractéristique) à un modèle spécifique, elle sera supprimée des autres modèles. Cette suppression ajoute de la pertinence à la liste d’expressions pour le modèle auquel elle est appliquée, ce qui contribue à améliorer l’exactitude qu’elle apporte dans le modèle.

L’indicateur `enabledForAllModels` contrôle l’étendue de ce modèle dans l’API.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Comment utiliser une liste d’expressions

[Créez une liste d’expressions](luis-how-to-add-features.md) lorsque votre intention ou entité comprend des mots ou des expressions qui sont importants, par exemple :

* Terminologie du secteur
* Argot
* Abréviations
* Termes propres à l’entreprise
* Mots provenant d’une autre langue, mais qui sont fréquemment utilisés dans votre application
* Mots clés et expressions dans vos exemples d’énoncés

N’ajoutez **pas** tous les mots ou expressions possibles. Au lieu de cela, ajoutez quelques mots ou expressions à la fois, puis effectuez à nouveau l’apprentissage et publiez. À mesure que la liste s’agrandit, vous constaterez peut-être que certains termes ont de nombreuses formes (synonymes). Faites-en une autre liste.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Quand utiliser une entité comme caractéristique

Une entité peut être ajoutée en tant que caractéristique au niveau de l’intention ou de l’entité.

### <a name="entity-as-a-feature-to-an-intent"></a>Entité en tant que caractéristique d’une intention

Ajoutez une entité en tant que descripteur (caractéristique) à une intention lorsque la détection de cette entité est significative pour l’intention.

Par exemple, si l’intention vise à réserver un vol et que l’entité désigne les informations du ticket (telles que le nombre de sièges, l’origine et la destination), trouver l’entité d’informations du ticket doit alors ajouter du poids à la prédiction de l’intention de réservation du vol.

### <a name="entity-as-a-feature-to-another-entity"></a>Entité en tant que caractéristique d’une autre entité

Une entité (A) doit être ajoutée en tant que caractéristique à une autre entité (B) lorsque la détection de cette entité (A) est importante pour la prédiction de l’entité (B).

Par exemple, si l’entité adresse postale (A) est détectée, trouver l’adresse postale (A) ajoute du poids à la prédiction pour l’entité adresse d’expédition (B).

## <a name="best-practices"></a>Meilleures pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* [Étendre](schema-change-prediction-runtime.md) vos modèles d’application au niveau du runtime de prédiction
* Consultez [Ajouter des fonctionnalités](luis-how-to-add-features.md) pour découvrir comment ajouter des fonctionnalités à votre application LUIS.
