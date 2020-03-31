---
title: Migrer vers une entité issue du Machine Learning v3
titleSuffix: Azure Cognitive Services
description: La création v3 offre un nouveau type d’entité, l’entité issue du Machine Learning, ainsi que la possibilité d’ajouter des relations à l’entité issue du Machine Learning et à d’autres entités ou fonctionnalités de l’application.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563821"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrer vers l’entité de création v3

La création v3 offre un nouveau type d’entité, l’entité issue du Machine Learning, ainsi que la possibilité d’ajouter des relations à l’entité issue du Machine Learning et à d’autres entités ou fonctionnalités de l’application.

## <a name="entities-are-decomposable-in-v3"></a>Entités décomposables dans la v3

Les entités créées avec les API de création v3, soit à l’aide des [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) soit avec le [portail en version préliminaire](https://preview.luis.ai/), permettent de créer un modèle d’entité en couche avec un parent et des enfants. Le parent est connu sous le nom **d’entité issue du Machine Learning** et les enfants sont appelés **sous-composants** de l’entité issue du Machine Learning.

Chaque sous-composant est également une entité issue du Machine Learning, mais avec les options de configuration supplémentaires des contraintes et des descripteurs.

* Les **contraintes** sont des règles de correspondance de texte exactes qui garantissent qu’une entité est extraite lorsqu’elle correspond à une règle. La règle est définie par une entité de correspondance de texte exacte, actuellement : une [entité prédéfinie](luis-reference-prebuilt-entities.md) , une [entité d’expression régulière](reference-entity-regular-expression.md) ou une [entité de liste](reference-entity-list.md).
* Les **descripteurs** sont des [fonctionnalités](luis-concept-feature.md), comme des listes d’expressions ou des entités, utilisées pour indiquer fortement l’entité.

La création v3 offre un nouveau type d’entité, l’entité issue du Machine Learning, ainsi que la possibilité d’ajouter des relations à l’entité issue du Machine Learning et à d’autres entités ou fonctionnalités de l’application.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Comparaison entre ces nouvelles relations et la création v2

La création v2 offrait des entités hiérarchiques et composites, ainsi que des rôles et des fonctionnalités pour accomplir cette même tâche. Étant donné que les entités, les fonctionnalités et les rôles n’étaient pas explicitement liés les uns aux autres, il était difficile de comprendre comment LUIS impliquait les relations lors de la prédiction.

Avec la v3, la relation est explicite et conçue par les auteurs de l’application, qui peuvent ainsi :

* voir visuellement comment LUIS prédit ces relations, dans les exemples d’énoncés ;
* tester ces relations avec le [volet de test interactif](luis-interactive-test.md) ou sur le point de terminaison ;
* utiliser ces relations dans l’application cliente, au moyen d’un [objet .json](reference-entity-machine-learned-entity.md) bien structuré, nommé et imbriqué.

## <a name="planning"></a>Planification

Prenez en compte les points suivants dans votre stratégie de migration :

* Sauvegardez votre application LUIS, puis effectuez la migration sur une application distincte. Le fait de disposer d’une application v2 et d’une application v3 disponibles en même temps permet de valider les modifications requises et l’impact sur les résultats de prédiction.
* Capturez les métriques de réussite des prédictions actuelles.
* Capturez les informations actuelles du tableau de bord sous la forme d’un instantané de l’état de l’application.
* Passez en revue les intentions, les entités, les listes d’expressions, les modèles et les tests par lot existants.
* Les éléments suivants peuvent être migrés **sans modification** :
    * Intentions
    * Entités
        * Entité d’expression régulière
        * Entité de liste
    * Fonctionnalités
        * Liste d’expressions
* Les éléments suivants doivent être migrés **avec modifications** :
    * Entités
        * Entité hiérarchique
        * Entité composite
    * Rôle (ils ne peuvent être appliqués qu’à une entité (parente) issue du Machine Learning, et non à des sous-composants)
    * Tests par lots et modèles qui utilisent les entités hiérarchiques et composites

Lorsque vous concevez votre plan de migration, laissez-vous du temps pour passer en revue les entités issues du Machine Learning finales, une fois que toutes les entités hiérarchiques et composites ont été migrées. Bien qu’une migration directe fonctionne, après modification et vérification de vos résultats de test par lot, ainsi que du JSON de prédiction, le JSON plus unifié est susceptible de vous amener à apporter des modifications afin que les informations finales fournies à l’application côté client soient organisées différemment. Le principe est similaire à la refactorisation du code et doit être traité suivant le processus de révision de votre organisation.

Si vous ne disposez pas de tests par lots pour votre modèle v2 et que vous migrez les tests par lots vers le modèle v3, vous ne pourrez pas valider l’impact de la migration sur les résultats de prédiction du point de terminaison.

## <a name="migrating-from-v2-entities"></a>Migration à partir d’entités v2

Lorsque vous commencez à migrer vers le modèle de création v3, réfléchissez à la façon dont vous passerez à l’entité issue du Machine Learning et à ses sous-composants, notamment aux contraintes et aux descripteurs.

Le tableau suivant présente les entités qui doivent migrer d’une conception d’entité v2 à v3.

|Type d’entité de création v2|Type d’entité de création v3|Exemple|
|--|--|--|
|Entité composite|Entité issue du Machine Learning|[En savoir plus](#migrate-v2-composite-entity)|
|Entité hiérarchique|Rôle de l’entité issue du Machine Learning|[En savoir plus](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migration d’une entité composite v2

Chacun des enfants de l’entité composite v2 doit être représenté par un sous-composant de l’entité issue du Machine Learning v3. Si l’enfant composite est une entité prédéfinie, une expression régulière ou une entité de liste, il doit être appliqué sous forme de **contrainte** sur le sous-composant représentant l’enfant.

Considérations à prendre en compte pour planifier la migration d’une entité composite vers une entité issue du Machine Learning :
* Les entités enfants ne sont pas utilisables dans des modèles.
* Les entités enfants ne sont plus partagées.
* Les entités enfants doivent être étiquetées si elles n’étaient pas issues du Machine Learning à l’origine.

### <a name="existing-descriptors"></a>Descripteurs existants

Toute liste d’expressions utilisée pour amplifier les mots dans l’entité composite doit être appliquée comme descripteur à l’entité issue du Machine Learning (parent), à l’entité de sous-composant (enfant) ou à l’intention (si la liste d’expressions ne s’applique qu’à une seule intention). Planifiez l’ajout du descripteur à l’entité qu’il devrait améliorer le plus. N’ajoutez pas le descripteur de façon générique à l’entité issue du Machine Learning (parent), s’il augmente considérablement la prédiction d’un sous-composant (enfant).

### <a name="new-descriptors"></a>Nouveaux descripteurs

Dans la création v3, ajoutez une étape de planification pour évaluer les entités comme des descripteurs possibles de toutes les entités et intentions.

### <a name="example-entity"></a>Exemple d’entité

Il s’agit simplement d’un exemple d’entité. La migration de votre propre entité est susceptible d’impliquer d’autres considérations.

Imaginez une entité composite v2 pour la modification d’une commande `order` de pizza qui utilise :
* une entité prédéfinie datetimeV2 pour le délai de livraison ;
* une liste d’expressions pour améliorer certains mots, comme pizza, tarte, croûte et garniture ;
* une entité de liste pour détecter les garnitures, par exemple les champignons, les olives, les lardons.

Voici un exemple d’énoncé pour cette entité :

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Le tableau suivant illustre la migration :

|Modèles v2|Modèles v3|
|--|--|
|Parent : entité composant nommée `Order`|Parent : entité issue du Machine Learning nommée `Order`|
|Enfant : entité prédéfinie datetimeV2|* Migrer une entité prédéfinie sur une nouvelle application.<br>* Ajouter une contrainte sur le parent de l’entité prédéfinie datetimeV2.|
|Enfant : entité de liste pour les garnitures|* Migrer une entité de liste sur une nouvelle application.<br>* Ajouter ensuite une contrainte sur le parent de l’entité de liste.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrer une entité hiérarchique v2

Dans la création v2, une entité hiérarchique était fournie avant les rôles existants dans LUIS. Les deux répondaient au même objectif d’extraction d’entités en fonction de l’utilisation du contexte. Si vous avez des entités hiérarchiques, vous pouvez les considérer comme des entités simples associées à des rôles.

Dans la création v3 :
* Un rôle peut être appliqué à l’entité issue du Machine Learning (parent).
* Un rôle ne peut pas être appliqué à des sous-composants.

Il s’agit simplement d’un exemple d’entité. La migration de votre propre entité est susceptible d’impliquer d’autres considérations.

Prenons l’exemple d’une entité hiérarchique v2 pour la modification d’une commande `order` de pizza :
* où chaque enfant détermine soit une garniture d’origine, soit la garniture finale

Voici un exemple d’énoncé pour cette entité :

`Change the topping from mushrooms to olives`

Le tableau suivant illustre la migration :

|Modèles v2|Modèles v3|
|--|--|
|Parent : entité composant nommée `Order`|Parent : entité issue du Machine Learning nommée `Order`|
|Enfant : entité hiérarchique avec garniture de pizza d’origine et finale|* Ajouter un rôle à `Order` pour chaque garniture.|

## <a name="next-steps"></a>Étapes suivantes

* [Ressources pour les développeurs](developer-reference-resource.md)
