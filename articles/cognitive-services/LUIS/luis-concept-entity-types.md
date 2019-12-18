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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976959"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entités et leur objectif dans LUIS

L’objectif principal des entités est de donner à l’application cliente une extraction prévisible des données. Un objectif secondaire _facultatif_ est d’améliorer la prédiction de l’intention ou d’autres entités à l’aide de descripteurs.

Il existe deux types d’entités :

* issue de l’apprentissage automatique, à partir du contexte
* non issue de machine learning, pour les correspondances de texte exactes, les correspondances de modèle ou la détection par des entités prédéfinies

Les entités issues de machine learning fournissent la plus large gamme de choix d’extraction de données. Les entités non issues de machine learning fonctionnent par correspondance de texte et peuvent être utilisées indépendamment ou comme une [contrainte](#design-entities-for-decomposition) sur une entité issue de machine learning.

## <a name="entities-represent-data"></a>Les entités représentent les données

Les entités sont des données que vous souhaitez extraire de l’énoncé, telles que des noms, des dates, des noms de produit ou des groupes de mots significatifs. Un énoncé peut inclure plusieurs entités ou aucune. Une application cliente _peut_ avoir besoin des données pour accomplir sa tâche.

Les entités doivent être étiquetées de manière cohérente sur tous les énoncés d’entraînement pour chaque intention dans un modèle.

 Vous pouvez définir vos propres entités ou utiliser des entités prédéfinies afin de gagner du temps pour les concepts courants comme [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md) et [numéro de téléphone](luis-reference-prebuilt-phonenumber.md).

|Énoncé|Entité|Données|
|--|--|--|
|Acheter 3 tickets pour New York|Nombre prédéfini<br>Location.Destination|3<br>New York|
|Acheter un ticket de New York à Londres pour le 5 mars|Location.Origin<br>Location.Destination<br>datetimeV2 prédéfini|New York<br>Londres<br>5 mars 2018|

### <a name="entities-are-optional"></a>Les entités sont facultatives

Les intentions sont obligatoires mais les entités sont facultatives. Vous n’avez pas besoin créer des entités pour chaque concept dans votre application, mais uniquement pour ceux dont l’application a besoin pour effectuer une action.

Si vos énoncés n’ont pas les données requises par l’application cliente, vous n’avez pas besoin d’ajouter des entités. À mesure que votre application se développe et qu’un nouveau besoin de données est identifié, vous pouvez ajouter des entités appropriées à votre modèle LUIS par la suite.

## <a name="entity-compared-to-intent"></a>Comparaison entre entité et intention

L’entité représente un concept de données à l’intérieur de l’énoncé que vous souhaitez extraire.

Un énoncé peut éventuellement inclure des entités. En comparaison, la prédiction de l’intention d’un énoncé est _obligatoire_ et représente l’énoncé entier. LUIS exige que les exemples d’énoncés soient contenus dans une intention.

Examinez les 4 énoncés suivants :

|Énoncé|Intention prédite|Entités extraites|Explication|
|--|--|--|--|
|Aide|help|-|Rien à extraire.|
|Envoyer quelque chose|sendSomething|-|Rien à extraire. Le modèle n’a pas été entraîné pour extraire `something` dans ce contexte, et il n’y a pas non plus de destinataire.|
|Envoyer un cadeau à Bob|sendSomething|`Bob`, `present`|Le modèle a été entraîné avec l’entité prédéfinie [personName](luis-reference-prebuilt-person.md), qui a extrait le nom `Bob`. Une entité issue de machine learning a été utilisée pour extraire `present`.|
|Envoyer une boîte de chocolats à Bob|sendSomething|`Bob`, `box of chocolates`|Les deux éléments de données importants, `Bob` et `box of chocolates`, ont été extraits par des entités.|

## <a name="design-entities-for-decomposition"></a>Concevez des entités pour la décomposition

Une conception d’entité judicieuse consiste à transformer votre entité de niveau supérieur en entité issue de machine learning. Cela permet de modifier la conception de votre entité au fil du temps et l’utilisation de **sous-composants** (entités enfants), éventuellement avec des **contraintes** et des **descripteurs**, pour décomposer l’entité de niveau supérieur en composants nécessaires à l’application cliente.

Concevoir pour la décomposition permet à LUIS de restituer un degré élevé de résolution des entités à votre application cliente. Cela permet à votre application cliente de se concentrer sur les règles métier et de confier la résolution des données à LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Les entités issues de l’apprentissage automatique sont des collections de données primaires

Les [**entités issues de machine learning**](tutorial-machine-learned-entity.md) constituent l’unité de données de niveau supérieur. Les sous-composants sont des entités enfants d’entités issues de l’apprentissage automatique.

Une entité issue de machine learning est déclenchée en fonction du contexte appris par le biais d’énoncés d’entraînement. Les **contraintes** sont des règles facultatives appliquées à une entité issue de machine learning qui limitent davantage le déclenchement en fonction de la définition de correspondance de texte exacte d’une entité non issue de machine learning comme [Liste](reference-entity-list.md) ou [Expression régulière](reference-entity-regular-expression.md). Par exemple, une entité issue de machine learning `size` peut avoir une contrainte d’une entité de liste `sizeList` qui limite l’entité `size` à se déclencher uniquement en présence de valeurs contenues dans l’entité `sizeList`.

Les [**descripteurs**](luis-concept-feature.md) sont des fonctionnalités appliquées afin d’améliorer la pertinence des mots ou expressions pour la prédiction. Ces fonctionnalités sont appelées *descripteurs* parce qu’elles sont utilisées pour *décrire* une intention ou une entité. Les descripteurs décrivent des caractéristiques ou des attributs de données distinctifs, tels que des expressions ou des mots importants que Luis examine et apprend.

Lorsque vous créez une fonctionnalité de liste d’expressions dans votre application LUIS, elle est activée globalement par défaut et s’applique uniformément à l’ensemble des intentions et des entités. Toutefois, si vous appliquez la liste d’expressions comme descripteur (fonctionnalité) d’une entité issue de machine learning (ou *modèle*), sa portée diminue pour s’appliquer uniquement à ce modèle et n’est plus utilisée avec aucun autre modèle. L’utilisation d’une liste d’expressions comme descripteur d’un modèle participe à la décomposition en aidant à la précision du modèle auquel elle est appliquée.

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
|[**Issue de l’apprentissage automatique**](tutorial-machine-learned-entity.md)|Les entités apprises par la machine apprennent à partir du contexte dans l’énoncé. Regroupement parent d’entités, quel que soit le type d’entité. Cela rend la variation du placement dans les exemples d’énoncés significative. |
|[**Liste**](reference-entity-list.md)|Liste d’éléments et de leurs synonymes extraits avec une **correspondance exacte du texte**.|
|[**Pattern.any**](reference-entity-pattern-any.md)|Entité dont la fin est difficile à déterminer. |
|[**Prédéfinie**](luis-reference-prebuilt-entities.md)|Déjà entraînée à extraire un type spécifique de données comme une URL ou un e-mail. Certaines de ces entités prédéfinies dans le projet open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Si votre culture ou entité spécifique n’est pas encore prise en charge, vous pouvez contribuer au projet.|
|[**Expression régulière**](reference-entity-regular-expression.md)|Utilise l’expression régulière pour une **correspondance exacte du texte**.|

## <a name="extracting-contextually-related-data"></a>Extraction de données relatives au contexte

Un énoncé peut contenir au moins deux occurrences d’une entité où la signification des données est basée sur le contexte dans l’énoncé. Par exemple, un énoncé pour la réservation d’un vol ayant deux emplacements : l’origine et la destination.

`Book a flight from Seattle to Cairo`

Les deux exemples d’une entité `location` doivent être extraits. L’application cliente doit connaître le type d’emplacement pour chacun d’entre eux afin de finaliser l’achat du ticket.

Il existe deux techniques d’extraction de données relatives au contexte :

 * L’entité `location` est une entité issue de machine learning qui utilise deux entités de sous-composants pour capturer `origin` et `destination` (par défaut)
 * L’entité `location` utilise deux **rôles** `origin` et `destination`

Plusieurs entités peuvent exister dans un énoncé et être extraites sans utiliser la décomposition ni les rôles si le contexte dans lequel elles sont utilisées n’a pas d’importance. Par exemple, si l’énoncé inclut une liste d’emplacements, `I want to travel to Seattle, Cairo, and London.`, il s’agit d’une liste où chaque élément n’a aucune autre signification.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Utilisation d’entités de sous-composants d’une entité issue de machine learning pour définir le contexte

Vous pouvez utiliser une [**entité issue de machine learning**](tutorial-machine-learned-entity.md) pour extraire les données qui décrivent l’action de réservation d’un vol, puis décomposer l’entité de niveau supérieur en composants distincts nécessaires à l’application cliente.

Dans cet exemple, `Book a flight from Seattle to Cairo`, l’entité de niveau supérieur peut être `travelAction` et étiquetée pour extraire `flight from Seattle to Cairo`. Deux entités de sous-composants sont ensuite créées, appelées `origin` et `destination`, avec une contrainte appliquée de l’entité `geographyV2` prédéfinie. Dans les énoncés d’entraînement, les entités `origin` et `destination` sont étiquetées de manière appropriée.

### <a name="using-entity-role-to-define-context"></a>Utilisation du rôle d’entité pour définir le contexte

Un rôle est un alias nommé pour une entité, basé sur le contexte dans l’énoncé. Un rôle peut être utilisé avec un type d’entité prédéfini ou personnalisé, à la fois dans les exemple d’énoncés et de modèles. Dans cet exemple, l’entité `location` a besoin de deux rôles, `origin` et `destination`, et les deux doivent être marqués dans les exemples d’énoncés.

Si LUIS trouve le `location`, mais ne peut pas déterminer le rôle, l’entité de l’emplacement est tout de même retournée. L’application cliente doit donner suite avec une question pour déterminer à quel type d’emplacement l’utilisateur voulait faire référence.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Si vous avez besoin de plus que le nombre maximal d’entités

Si vous en avez besoin de plus que la limite autorisée, contactez le support. Pour cela, rassemblez des informations détaillées sur votre système, accédez au site web [LUIS](luis-reference-regions.md#luis-website), puis sélectionnez **Support**. Si votre abonnement Azure comprend des services de support, contactez le [support technique Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>État de prédiction d’entité

Le portail LUIS indique quand l’entité, dans un exemple d’énoncé, a une prédiction d’entité différente de celle de l’entité que vous avez sélectionnée. Ce score différent est basé sur le modèle entraîné actuel.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts relatifs aux bons [énoncés](luis-concept-utterance.md).

Consulter [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.

Consultez le [tutoriel : Extraire des données structurées à partir d’un énoncé utilisateur avec des entités issues du Machine Learning dans LUIS](tutorial-machine-learned-entity.md) pour apprendre à extraire des données structurées d’un énoncé à l’aide de l’entité issue de machine learning.
 
