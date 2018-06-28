---
title: Didacticiel de création d’une application LUIS retournant des expressions clés - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez apprendre à ajouter et à retourner une entité keyPhrase à votre application LUIS pour analyser les énoncés du thème principal.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264613"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Didacticiel : Créer une application qui retourne les données de l’entité keyPhrase trouvées dans les énoncés
Dans ce didacticiel, vous allez créer une application montrant comment extraire le thème principal des énoncés.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités keyPhrase 
> * Créer une application LUIS pour le domaine des ressources humaines
> * Ajouter l’intention _None_ ainsi que des exemples d’énoncés
> * Ajouter une entité keyPhrase pour extraire le contenu d’un énoncé
> * Effectuer l’apprentissage de l’application et la publier
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

Pour cet article, vous pouvez utiliser le compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="keyphrase-entity-extraction"></a>Extraction de l’entité keyPhrase
Le thème principal est fourni par l’entité prédéfinie, **keyPhrase**. Cette entité retourne le thème principal de l’énoncé.

Les énoncés suivants présentent des exemples d’expressions clés :

|Énoncé|Valeurs d’entité keyPhrase|
|--|--|
|Y-a-t-il un nouveau régime d’assurance-maladie qui propose une franchise moins élevée l’année suivante ?|« franchise moins élevée »<br>« nouveau régime d’assurance-maladie »<br>« année »|
|La thérapie visuelle est-elle couverte par le régime d’assurance-maladie à franchise élevée ?|« régime d’assurance-maladie à franchise élevée »<br>« thérapie visuelle »|

Votre chatbot peut prendre en compte ces valeurs en plus de toutes les autres entités extraites, lorsque vous décidez de l’étape suivante de la conversation.

## <a name="download-sample-app"></a>Télécharger un exemple d’application
Téléchargez l’application [Ressources humaines](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) et enregistrez-la dans un fichier pourvu de l’extension *.json. Cet exemple d’application reconnaît les énoncés pertinents pour les avantages sociaux des employés, les organigrammes et les ressources physiques.

## <a name="create-a-new-app"></a>Créer une application
1. Connectez-vous au site web [LUIS][LUIS]. Veillez à vous connecter à la [région][LUIS-regions] où vous avez besoin de points de terminaison LUIS publiés.

2. Sur le site web [LUIS][LUIS], sélectionnez **Import new app** (Importer une nouvelle application) pour importer l’application Ressources humaines téléchargée dans la section précédente. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Capture d’écran de la page Liste des applications")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. Dans la boîte de dialogue **Import new app** (Importer une nouvelle application), nommez l’application `Human Resources with Key Phrase entity`. 

    ![Image de la boîte de dialogue Créer une application](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Lorsque le processus de création de l’application est terminé, LUIS affiche la liste des intentions.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Capture d’écran de la page Liste des intentions")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Ajouter une entité keyPhrase 
Ajoutez une entité prédéfinie keyPhrase pour extraire le thème des énoncés.

1. Dans le menu gauche, sélectionnez **Entités**.

    [ ![Capture d’écran du menu Entités mis en surbrillance dans le volet de navigation gauche de la section Build (Générer)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Sélectionnez **Manage prebuilt entities** (Gérer les entités prédéfinies).

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. Dans la boîte de dialogue contextuelle, sélectionnez **keyPhrase**, puis **Terminé**. 

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Effectuer l’apprentissage de l’application LUIS
LUIS ne connaît pas cette modification apportée au modèle tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Capture d’écran du bouton Effectuer l’apprentissage mis en surbrillance](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Capture d’écran de la barre de notification de réussite de l’apprentissage ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Publier l’application vers un point de terminaison

1. Dans le volet de navigation supérieur droit, sélectionnez **Publier**.

    ![Capture d’écran de la page Entités avec le bouton Publier développé ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-an-utterance"></a>Interroger le point de terminaison avec un énoncé

1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    ![Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Is there a new medical plan with a lower deductible offered next year?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, avec la détection d’entité keyPhrase, a identifié une intention de type requête en langage naturel et retourné les données extraites contenant le thème principal. 

À présent, votre chatbot possède suffisamment d’informations pour déterminer l’étape suivante de la conversation. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité keyPhrase de l’énoncé pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour ce faire, sélectionnez le menu représentant trois points (...) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application qui retourne un sentiment avec une prédiction d’intention](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
