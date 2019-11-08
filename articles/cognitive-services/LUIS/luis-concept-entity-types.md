---
title: Types d’entités - LUIS
titleSuffix: Azure Cognitive Services
description: 'Les entités extraient des données à partir de l’énoncé. Les types d’entités vous donnent une extraction prévisible des données. Il existe deux types d’entités : celles apprises par la machine et celles non apprises par la machine. Il est important de savoir avec quel type d’entité vous travaillez dans les énoncés.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487597"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entités et leur objectif dans LUIS

L’objectif principal des entités est de donner à l’application cliente une extraction prévisible des données. Un objectif secondaire _facultatif_ est d’améliorer la prédiction de l’intention à l’aide de descripteurs. 

Il existe deux types d’entités : 

* issue de l’apprentissage automatique, à partir du contexte
* non issue de l’apprentissage automatique, pour les correspondances de texte exactes

Commencez toujours par une entité issue de l’apprentissage automatique, car elle fournit le plus large éventail de choix en matière d’extraction de données.

## <a name="entity-compared-to-intent"></a>Comparaison entre entité et intention

L’entité représente un concept de données à l’intérieur de l’énoncé que vous souhaitez extraire. 

Examinez les trois énoncés suivants :

|Énoncé|Données extraites|Explication|
|--|--|--|
|`Help`|-|Rien à extraire.|
|`Send Bob a present`|Bob, présent|Bob est sans aucun doute important pour accomplir la tâche. Le présent peut être une information suffisante ou le bot peut avoir besoin de clarifier ce qu’est le présent avec une question de suivi.|
|`Send Bob a box of chocolates.`|Les deux données importantes, Bob et la boîte de chocolats, sont importantes pour compléter la demande de l’utilisateur.|

Un énoncé peut inclure plusieurs entités ou aucune. Une application cliente _peut_ avoir besoin de l’entité pour accomplir sa tâche. 

En comparaison, la prédiction de l’intention d’un énoncé est _obligatoire_ et représente l’énoncé entier. LUIS exige que les exemples d’énoncés soient contenus dans une intention. Si l’intention première de l’énoncé n’est pas importante pour l’application cliente, ajoutez tous les énoncés à l’intention Aucun. 

Si vous constatez, plus tard dans le cycle de vie de l’application, que vous souhaitez décomposer les énoncés, vous pouvez facilement le faire. Il peut s’agir d’organiser les énoncés pendant la rédaction ou d’utiliser l’intention prédite dans l’application cliente. 

Il n’est pas nécessaire d’utiliser l’intention prédite dans l’application cliente, mais elle est retournée dans le cadre de la réponse du point de terminaison de prédiction.

## <a name="entities-represent-data"></a>Les entités représentent les données

Les entités sont des données que vous voulez extraire à partir de l’énoncé. Il peut s’agir d’un nom, d’une date, d’un nom de produit ou d’un groupe de mots. 

|Énoncé|Entité|Données|
|--|--|--|
|Acheter 3 tickets pour New York|Nombre prédéfini<br>Location.Destination|3<br>New York|
|Acheter un ticket de New York à Londres pour le 5 mars|Location.Origin<br>Location.Destination<br>datetimeV2 prédéfini|New York<br>Londres<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Les entités sont facultatives mais fortement recommandées

Les intentions sont obligatoires mais les entités sont facultatives. Vous n’avez pas besoin créer des entités pour chaque concept dans votre application, mais uniquement pour ceux dont l’application a besoin pour effectuer une action. 

Si vos énoncés ne contiennent pas les détails dont votre bot a besoin pour continuer, il est inutile de les ajouter. Vous pouvez les ajouter ultérieurement, au fur et à mesure que votre application arrive à maturité. 

Si vous ne savez pas comment utiliser les informations, ajoutez quelques entités prédéfinies courantes, comme [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md) et [phone number](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Concevez des entités pour la décomposition

Commencez la conception de votre entité par une entité issue de l’apprentissage automatique. Cela facilite la croissance de la conception et la modification de votre entité au fil du temps. Ajoutez des **sous-composants** (entités enfants) avec des **contraintes** et des **descripteurs** pour terminer la conception de l’entité. 

Concevoir pour la décomposition permet à LUIS de restituer un degré élevé de résolution des entités à votre application cliente. Cela permet à votre application cliente de se concentrer sur les règles d’entreprise et de confier la résolution des données à LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Les entités issues de l’apprentissage automatique sont des collections de données primaires

Les entités issues de l’apprentissage automatique sont l’unité de données de niveau supérieur. Les sous-composants sont des entités enfants d’entités issues de l’apprentissage automatique. 

Les **contraintes** sont des entités de correspondance de texte exacte qui appliquent des règles pour identifier et extraire des données. Les **descripteurs** sont des fonctionnalités appliquées pour améliorer la pertinence des mots ou expressions pour la prédiction.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Types d’entités

Choisissez l’entité en fonction de la façon dont les données doivent être extraites et être représentées une fois extraites.

|Type d’entité|Objectif|
|--|--|
|[**Issue de l’apprentissage automatique**](#composite-entity)|Regroupement parent d’entités, quel que soit le type d’entité. Les entités apprises par la machine apprennent à partir du contexte dans l’énoncé. Cela rend la variation du placement dans les exemples d’énoncés significative. |
|[**Liste**](#list-entity)|Liste d’éléments et de leurs synonymes extraits avec une **correspondance exacte du texte**.|
|[**Pattern.any**](#patternany-entity)|Entité dont la fin est difficile à déterminer. |
|[**Prédéfinie**](#prebuilt-entity)|Déjà entraînée à extraire un type spécifique de données comme une URL ou un e-mail. Certaines de ces entités prédéfinies dans le projet open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Si votre culture ou entité spécifique n’est pas encore prise en charge, vous pouvez contribuer au projet.|
|[**Expression régulière**](#regular-expression-entity)|Utilise l’expression régulière pour une **correspondance exacte du texte**.|

### <a name="entity-role-defines-context"></a>Contexte défini par le rôle de l’entité

Le rôle d’une entité est l’alias nommé en fonction du contexte dans l’énoncé. Par exemple, un énoncé pour la réservation d’un vol ayant deux emplacements : l’origine et la destination.

`Book a flight from Seattle to Cairo`

Les deux exemples d’une entité `location` doivent être extraits. L’application cliente doit connaître le type d’emplacement pour chacun d’entre eux afin de finaliser l’achat du ticket. L’entité `location` a besoin de deux rôles,`origin` et `destination`, et les deux doivent être marqués dans les exemples d’énoncés. 

Si LUIS trouve le `location`, mais ne peut pas déterminer le rôle, l’entité de l’emplacement est tout de même retournée. L’application cliente doit donner suite avec une question pour déterminer à quel type d’emplacement l’utilisateur voulait faire référence. 

Plusieurs entités peuvent exister dans un énoncé et être extraites sans utiliser de rôles. Si le contexte de la phrase indique la valeur de l’entité, alors un rôle doit être utilisé.

Si l’énoncé inclut une liste d’emplacements, `I want to travel to Seattle, Cairo, and London.`, il s’agit d’une liste où chaque élément n’a qu’une signification. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Si vous avez besoin de plus que le nombre maximal d’entités 

Si vous en avez besoin de plus que la limite autorisée, contactez le support. Pour cela, rassemblez des informations détaillées sur votre système, accédez au site web [LUIS](luis-reference-regions.md#luis-website), puis sélectionnez **Support**. Si votre abonnement Azure comprend des services de support, contactez le [support technique Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>État de prédiction d’entité

Le portail LUIS indique quand l’entité, dans un exemple d’énoncé, a une prédiction d’entité différente de celle de l’entité que vous avez sélectionnée. Ce score différent est basé sur le modèle entraîné actuel. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts relatifs aux bons [énoncés](luis-concept-utterance.md). 

Consultez [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.
