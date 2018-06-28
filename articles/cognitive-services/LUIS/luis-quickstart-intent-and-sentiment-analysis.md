---
title: Didacticiel permettant de créer une application LUIS retournant une analyse des sentiments - Azure | Microsoft Docs
description: Dans ce didacticiel, découvrez comment ajouter une analyse des sentiments à votre application LUIS afin d’analyser les énoncés de sentiments positifs, négatifs et neutres.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265332"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Didacticiel : Créer une application qui retourne un sentiment avec une prédiction d’intention
Dans ce didacticiel, vous allez créer une application montrant comment extraire le sentiment positif, négatif et neutre des énoncés.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités hiérarchiques et les enfants issus du contexte 
> * Créer une application LUIS pour un domaine de voyage avec l’intention Bookflight
> * Ajouter l’intention _None_ et ajouter des exemples d’énoncés
> * Ajouter une entité hiérarchique avec des enfants d’origine et de destination
> * Former et publier l’application
> * Interroger un point de terminaison d’application pour voir la réponse JSON LUIS, y compris les enfants hiérarchiques 

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="sentiment-analysis"></a>analyse de sentiments
L’analyse des sentiments permet de déterminer si l’énoncé d’un utilisateur est positif, négatif ou neutre. 

Les énoncés suivants présentent des exemples de sentiments :

|Sentiment et score|Énoncé|
|:--|--|
|positive - 0,89 |La soupe et la salade étaient bonnes.|
|négatif - 0,07 |Je n’ai pas aimé l’entrée du dîner.|

L’analyse des sentiments est le paramètre d’application qui s’applique à chaque énoncé. Vous ne devez pas rechercher les mots indiquant le sentiment dans l’énoncé et les étiqueter. LUIS le fera pour vous.

## <a name="create-a-new-app"></a>Créer une application
1. Connectez-vous au site web [LUIS][LUIS]. Veillez à vous connecter à la [région][LUIS-regions] où vous avez besoin de points de terminaison LUIS publiés.

2. Sur le site web [LUIS][LUIS], sélectionnez **Créer une application**. 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Capture d’écran de la page Liste des applications")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. Dans la boîte de dialogue **Créer une application**, nommez l’application `Restaurant Reservations With Sentiment` et sélectionnez **Terminé**. 

    ![Image de la boîte de dialogue Créer une application](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Lorsque le processus de création d’application est terminé, LUIS affiche la liste des intentions contenant l’intension Aucun.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Capture d’écran de la page Liste des intentions")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Ajouter un domaine prédéfini
Ajoutez un domaine prédéfini pour pouvoir ajouter rapidement des intentions, des entités et des intentions étiquetées.

1. Sélectionnez **Prebuilt Domains** (Domaines prédéfinis) dans le menu de gauche.

    [ ![Capture d’écran du bouton de domaine prédéfini](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Sélectionnez **Ajouter un domaine** pour le domaine prédéfini **RestaurantReservation**. Attendez que le domaine soit ajouté.

    [ ![Capture d’écran de la liste de domaines prédéfinis](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. Sélectionnez **Intentions** dans le volet de navigation gauche. Ce domaine prédéfini contient une intention.

    [ ![Capture d’écran de la liste de domaines prédéfinis avec les intentions mises en surbrillance dans le volet de navigation gauche](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Sélectionnez l’intention **RestaurantReservation.Reserve**. 

    [ ![Capture d’écran de la liste des intentions avec RestaurantReservation.Reserve mis en surbrillance](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Basculez l’**affichage des entités** pour voir les nombreux énoncés fournis avec les entités spécifiques au domaine étiquetées.

    [ ![Capture d’écran de l’intention RestaurantReservation.Reserve avec l’affichage des entités basculé sur l’affichage du jeton mis en surbrillance](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Effectuer l’apprentissage de l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Capture d’écran du bouton Effectuer l’apprentissage mis en surbrillance](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Capture d’écran de la barre de notification de réussite de l’apprentissage ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurer l’application pour inclure l’analyse des sentiments
L’analyse des sentiments est activée sur la page **Publier**. 

1. Dans le volet de navigation supérieur droit, sélectionnez **Publier**.

    ![Capture d’écran de la page d’intention avec le bouton Publier développé ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Sélectionnez **Enable Sentiment Analysis** (Activer l’analyse des sentiments).

    ![Capture d’écran de la page Publier avec Enable Sentiment Analysis (Activer l’analyse des sentiments) mis en surbrillance ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-an-utterance"></a>Interroger le point de terminaison avec un énoncé

1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    ![Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Reserve table for  10 on upper level away from kitchen`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `RestaurantReservation.Reserve` avec l’analyse des sentiments extraite.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, avec l’analyse des sentiments activée, a identifié une intention de type requête en langage naturel et retourné les données extraites contenant le sentiment global sous forme de score. 

À présent, votre chatbot possède suffisamment d’informations pour déterminer l’étape suivante de la conversation. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de sentiment de l’énoncé pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour ce faire, sélectionnez le menu représentant trois points (...) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Appeler une API de point de terminaison LUIS avec C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
