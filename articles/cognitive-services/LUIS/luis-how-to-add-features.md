---
title: Fonctionnalités – LUIS
titleSuffix: Azure Cognitive Services
description: Utiliser Language Understanding (LUIS) pour ajouter des fonctionnalités d’application pouvant améliorer la détection ou la prédiction d’intentions et d’entités dans des catégories et des modèles
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592302"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Utiliser des fonctionnalités pour améliorer le signal de liste de mots

Vous pouvez ajouter des fonctionnalités à votre application LUIS pour améliorer sa précision. Ces fonctionnalités aident LUIS en indiquant que certains mots et expressions font partie d’un vocabulaire de domaine d’application.

Passez en revue les [concepts](luis-concept-feature.md) pour comprendre quand et pourquoi utiliser une fonctionnalité.

## <a name="add-phrase-list-as-a-feature"></a>Ajouter une liste d’expressions en tant que fonctionnalité

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications).
1. Sélectionnez **Créer**, puis **Fonctionnalités** dans le panneau gauche de votre application.

1. Dans la page **Fonctionnalités**, sélectionnez **+ Créer**.

1. Dans la boîte de dialogue **Créer une fonctionnalité de liste d’expressions**, entrez un nom comme `Cities`. Dans la zone **Valeur**, entrez des exemples de villes, comme `Seattle`. Vous pouvez taper une valeur à la fois, ou un ensemble de valeurs séparées par des virgules, puis appuyer sur **Entrée**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’ajout de la fonctionnalité (liste d’expressions) Villes](./media/luis-add-features/add-phrase-list-cities.png)

    Une fois que vous avez entré suffisamment de valeurs pour LUIS, des suggestions apparaissent. Vous pouvez cliquer sur **+ Ajouter** pour toutes les valeurs proposées ou sélectionner des termes individuels.

1. Laissez la case **Ces valeurs sont interchangeables** cochée si les expressions peuvent être utilisées indifféremment.

1. La liste d’expressions peut s’appliquer à l’application entière avec le paramètre **Global** ou à un modèle spécifique (intention ou entité). Si vous créez la liste d’expressions en tant que _fonctionnalité_ à partir d’une intention ou d’une entité, le bouton bascule n’est pas défini sur global. Dans ce cas, la signification du bouton bascule est que la fonctionnalité est propre à ce modèle uniquement, par conséquent _non globale_ à l’application.

1. Sélectionnez **Terminé**. La nouvelle fonctionnalité est ajoutée à la page **Fonctionnalités ML**.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Vous pouvez supprimer ou désactiver une liste d’expressions dans la barre d’outils contextuelle de la page **Fonctionnalités ML**.

## <a name="next-steps"></a>Étapes suivantes

Après l’ajout, la modification, la suppression ou la désactivation d’une fonctionnalité, [réentraînez et retestez l’application](luis-interactive-test.md) pour voir si les performances s’améliorent.
