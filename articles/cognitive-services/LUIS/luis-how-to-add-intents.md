---
title: Ajouter des intentions
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ajoutez des intentions à votre application LUIS pour identifier des groupes de questions ou de commandes qui ont les mêmes intentions.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: c8bcbe011dc4ff9841a06b914a90ae7f9a14ee74
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598497"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Ajouter des intentions pour déterminer l’intention de l’utilisateur des énoncés

Ajoutez des [intentions](luis-concept-intent.md) à votre application LUIS pour identifier des groupes de questions ou de commandes qui ont la même intention. 

Les intentions sont gérées à partir du volet gauche **Intents**, accessible en cliquant sur la section **Build** de la barre de navigation supérieure. 

## <a name="create-an-app"></a>Créer une application

1. Connectez-vous au portail [LUIS](https://www.luis.ai).

1. Sélectionnez **Créer une application**. 

1. Nommez la nouvelle application `MyHumanResourcesApp`. Sélectionnez la culture **English**. La description est facultative. 

1. Sélectionnez **Terminé**. 

## <a name="add-intent"></a>Ajouter une intention

1. L’application s’ouvre et la liste **Intents** (Intentions) apparaît.

1. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention).

1. Dans la boîte de dialogue **Create new intent**, entrez le nom de l’intention, `GetEmployeeInformation`, puis cliquez sur **Done**.

    ![Ajouter une intention](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Ajouter un exemple d’énoncé

Les exemples d’énoncés sont des exemples de texte de questions ou de commandes des utilisateurs. Pour l’apprentissage de Language Understanding (LUIS), vous devez ajouter des exemples d’énoncé à une intention.

1. Dans la page de détails d’intention **GetEmployeeInformation**, entrez un énoncé pertinent que vous attendez de vos utilisateurs, tel que `Does John Smith work in Seattle?`, dans la zone de texte sous le nom d’intention, puis appuyez sur Entrée.
 
    ![Capture d’écran de la page de détails Intents (Intentions), avec l’énoncé en surbrillance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS convertit tous les énoncés en minuscules et ajoute des espaces autour des jetons tels que les traits d’union.

## <a name="intent-prediction-discrepancy-errors"></a>Erreurs de différence de prédiction d’intention 

Un énoncé dans une intention peut présenter une différence de prédiction d’intention entre l’intention sélectionnée et le score de prédiction. LUIS signale cette différence en entourant en rouge l’**intention étiquetée** sur la ligne de l’exemple d’énoncé. 

![Capture d’écran de la page de détails Intents (Intentions), avec erreurs d’incohérence de prédiction d’énoncé](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

Dans la barre de navigation supérieure, sélectionnez **Train** (Entraîner). La différence de prédiction a désormais disparu.

## <a name="add-a-custom-entity"></a>Ajouter une entité personnalisée

Une fois qu’un énoncé a été ajouté à une intention, vous pouvez sélectionner du texte dans l’énoncé pour créer une entité personnalisée. Une entité personnalisée est un moyen de marquer du texte pour l’extraction, avec l’intention correcte. 

1. Dans l’énoncé, sélectionnez le mot `Seattle`. Des crochets sont dessinés autour du texte et un menu déroulant s’affiche. 

    ![Capture d’écran de la page de détails d’intention, avec création d’une entité personnalisée](./media/luis-how-to-add-intents/create-custom-entity.png) 

    Cet exemple sélectionne un mot unique pour le marquer en tant qu’entité. Vous pouvez marquer des mots et des expressions en tant qu’entités.

1. Dans la zone de texte supérieure du menu de configuration, entrez `Location`, puis sélectionnez **Create new entity** (Créer une entité). 

    ![Capture d’écran de la page de détails d’intention, avec création de nom d’entité personnalisée](./media/luis-how-to-add-intents/create-custom-entity-name.png) 

1. Dans la fenêtre contextuelle **What type of entity do you want to create?** (Quel type d’entité voulez-vous créer ?) pour la création d’entité, vérifiez que le **nom de l’entité** est _Location_ et le **type de l’entité**  est _Simple_. Sélectionnez **Terminé**.

## <a name="entity-prediction-discrepancy-errors"></a>Erreurs de différence de prédiction d’entité 

L’entité est soulignée en rouge pour signaler une [différence de prédiction d’entité](luis-how-to-add-example-utterances.md#entity-status-predictions). Comme il s’agit de la première occurrence d’une entité, il n’existe pas suffisamment d’exemples pour que LUIS ait un haut degré de confiance que ce texte soit marqué avec l’entité correcte. Cette différence disparaît quand l’application est entraînée. 

![Capture d’écran de la page de détails d’intention, avec nom d’entité personnalisée mis en surbrillance en bleu](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Le texte est mis en surbrillance en bleu, ce qui indique qu’il s’agit d’une entité.  

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie

Pour plus d’informations, consultez [Entité prédéfinie](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="using-the-contextual-toolbar"></a>Utilisation de la barre d’outils contextuelle

Quand un ou plusieurs exemples d’énoncés sont sélectionnés dans la liste, en cochant la case à gauche de l’énoncé, la barre d’outils au-dessus de la liste d’énoncés vous permet d’effectuer les actions suivantes :

* Réaffecter l’intention : déplacer des énoncés vers une autre intention
* Supprimer des énoncés
* Filtres d’entité : afficher uniquement les énoncés contenant des entités filtrées
* Afficher tout/erreurs uniquement : afficher les énoncés présentant des erreurs de prédiction ou afficher tous les énoncés
* Vue d’entités/jetons : afficher la vue des entités avec des noms d’entités ou afficher le texte brut d’énoncé
* Loupe : rechercher des énoncés contenant un texte spécifique

## <a name="working-with-an-individual-utterance"></a>Opérations sur un énoncé spécifique

Vous pouvez effectuer les actions suivantes en cliquant sur les points de suspension situés à droite de l’énoncé :

* Modifier : changer le texte de l’énoncé
* Supprimer : supprimer l’énoncé de l’intention. Si vous souhaitez conserver l’énoncé, une meilleure méthode consiste à le déplacer vers l’intention **None** 
* Ajouter un modèle : un modèle vous permet de prendre un énoncé courant et de marquer le texte remplaçable et le texte pouvant être ignoré, ce qui réduit le besoin d’énoncés supplémentaires dans l’intention. 

La colonne **Labeled intent** (Intention étiquetée) vous permet de changer l’intention de l’énoncé.

## <a name="train-your-app-after-changing-model-with-intents"></a>Former votre application après modification du modèle avec des intentions

Après avoir ajouté, modifié ou supprimé des intentions, [entraînez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications soient appliquées aux requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des [exemples d’énoncés](luis-how-to-add-example-utterances.md) avec des entités. 
