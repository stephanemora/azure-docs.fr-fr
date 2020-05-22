---
title: Modèles prédéfinis pour Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS inclut un ensemble de modèles prédéfinis pour ajouter rapidement des scénarios de conversation utilisateur courants.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 653882db4b62d7731123faf7b177da44dbd74e3f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584992"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Modèles prédéfinis pour les scénarios d’usage courants

LUIS inclut un ensemble de modèles prédéfinis pour ajouter rapidement des scénarios de conversation utilisateur courants. Il s’agit d’un moyen simple et rapide d’ajouter des fonctionnalités à votre application cliente conversationnelle sans avoir à concevoir les modèles pour ces fonctionnalités.

## <a name="add-a-prebuilt-domain"></a>Ajouter un domaine prédéfini

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications).

1. Sélectionnez **Prebuilt Domains** (Domaines prédéfinis) dans la barre d’outils gauche.

1. Recherchez le domaine à ajouter à l’application, puis sélectionnez le bouton **Add domain** (Ajouter un domaine).

    > [!div class="mx-imgBorder"]
    > ![Ajouter le domaine prédéfini Calendrier](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Ajouter une intention prédéfinie

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications).

1. Dans la page **Intents** (Intentions), sélectionnez **Add prebuilt domain intent** (Ajouter une intention de domaine prédéfinie) à partir de la barre d’outils au-dessus de la liste des intentions.

1. Sélectionnez l’intention **Utilities.Cancel** à partir de la boîte de dialogue contextuelle.

    > [!div class="mx-imgBorder"]
    > ![Ajouter une intention prédéfinie](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Cliquez sur le bouton **Terminé**.

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie
1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications).
1. Sur la gauche, sélectionnez **Entités**.

1. Dans la page **Entities** (Entités), sélectionnez **Add prebuilt entity** (Ajouter une entité prédéfinie).

1. Dans la boîte de dialogue **Add prebuilt entities** (Ajouter des entités prédéfinies), sélectionnez l’entité prédéfinie.

    > [!div class="mx-imgBorder"]
    > ![Boîte de dialogue Add prebuilt entities (Ajouter des entités prédéfinies)](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Sélectionnez **Terminé**. Une fois l’entité ajoutée, vous n’avez pas besoin d’effectuer l’apprentissage de l’application.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publier pour voir le modèle prédéfini à partir du point de terminaison de prédiction

La manière la plus simple de visualiser la valeur d’un modèle prédéfini consiste à interroger cette valeur à partir du point de terminaison publié.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entités contenant un jeton d’entité prédéfinie

Si l’une de vos entités issues du Machine Learning a besoin d’une fonctionnalité requise d’une entité prédéfinie, ajoutez-y un sous-composant, puis ajoutez une fonctionnalité _requise_ d’une entité prédéfinie.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Générer un modèle à partir d’un fichier .csv avec des API REST](./luis-tutorial-node-import-utterances-csv.md)
