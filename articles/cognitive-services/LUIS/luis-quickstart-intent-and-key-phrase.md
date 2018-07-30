---
title: Didacticiel de création d’une application LUIS retournant des expressions clés - Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à ajouter et à retourner une entité keyPhrase à votre application LUIS pour analyser les énoncés du thème principal.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: ccefb4c2890d74978f340778cfab7cad979c9802
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929547"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Tutoriel : 8. Ajouter une entité keyPhrase 
Dans ce tutoriel, vous allez utiliser une application montrant comment extraire les données principales des énoncés.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités keyPhrase 
> * Utiliser l’application LUIS pour le domaine des ressources humaines (RH) 
> * Ajouter une entité keyPhrase pour extraire le contenu d’un énoncé
> * Entraîner et publier l’application
> * Interroger un point de terminaison d’application pour voir la réponse JSON LUIS, y compris les expressions clé

Pour cet article, vous pouvez utiliser le compte [LUIS](luis-reference-regions.md#publishing-regions) gratuit afin de créer votre application LUIS.

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [entité simple](luis-quickstart-primary-and-secondary-data.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une application du site web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `keyphrase`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="keyphrase-entity-extraction"></a>Extraction de l’entité keyPhrase
Le thème principal est fourni par l’entité prédéfinie, **keyPhrase**. Cette entité retourne le thème principal de l’énoncé.

Les énoncés suivants présentent des exemples d’expressions clés :

|Énoncé|Valeurs d’entité keyPhrase|
|--|--|
|Y-a-t-il un nouveau régime d’assurance-maladie qui propose une franchise moins élevée l’année suivante ?|« franchise moins élevée »<br>« nouveau régime d’assurance-maladie »<br>« année »|
|La thérapie visuelle est-elle couverte par le régime d’assurance-maladie à franchise élevée ?|« régime d’assurance-maladie à franchise élevée »<br>« thérapie visuelle »|

Votre application cliente peut utiliser ces valeurs, ainsi que d’autres entités extraites, pour décider de l’étape suivante de la conversation.

## <a name="add-keyphrase-entity"></a>Ajouter une entité keyPhrase 
Ajoutez une entité prédéfinie keyPhrase pour extraire le thème des énoncés.

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. Dans le menu gauche, sélectionnez **Entités**.

    [ ![Capture d’écran du menu Entités mis en surbrillance dans le volet de navigation gauche de la section Build (Générer)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Sélectionnez **Manage prebuilt entities** (Gérer les entités prédéfinies).

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. Dans la boîte de dialogue contextuelle, sélectionnez **keyPhrase**, puis **Terminé**. 

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. Sélectionnez **Intentions** dans le menu de gauche, puis sélectionnez l’intention **Utilities.Confirm**. L’entité keyPhrase est étiquetée dans plusieurs énoncés. 

    [ ![Capture d’écran de l’intention Utilities.Confirm avec keyPhrases étiquetées dans les énoncés](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
La nouvelle version `keyphrase` de l’application doit être entraînée.  

1. En haut à droite du site web LUIS, sélectionnez le bouton **Former**.

    ![Effectuer l’apprentissage de l’application](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Entraînement réussi](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Publier l’application vers un point de terminaison

1. Dans le volet de navigation supérieur droit, sélectionnez **Publier**.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-an-utterance"></a>Interroger le point de terminaison avec un énoncé

1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    ![Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `does form hrf-123456 cover the new dental benefits and medical plan`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Lors de la recherche d’un formulaire, l’utilisateur a fourni plus d’informations que nécessaire pour trouver le formulaire. Les informations supplémentaires sont retournées en tant que **builtin.keyPhrase**. L’application client peut utiliser ces informations supplémentaires pour une question de suivi, telle que « Souhaitez-vous contacter un représentant des Ressources humaines à propos de la couverture dentaire » ou proposer un menu avec plus d’options, y compris « Plus d’informations sur la nouvelle couverture dentaire ou les remboursements médicaux ».

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, avec la détection d’entité keyPhrase, a identifié une intention de type requête en langage naturel et retourné les données extraites contenant le thème principal. 

À présent, votre chatbot possède suffisamment d’informations pour déterminer l’étape suivante de la conversation. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité keyPhrase de l’énoncé pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Sélectionnez **Mes applications** dans le menu en haut à gauche. Sélectionnez les points de suspension (***...***) à droite du nom de l’application dans la liste des applications, sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter l’analyse des sentiments à une application](luis-quickstart-intent-and-sentiment-analysis.md)