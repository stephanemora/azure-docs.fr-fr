---
title: Didacticiel permettant de créer une application LUIS retournant une analyse des sentiments - Azure | Microsoft Docs
description: Dans ce tutoriel, découvrez comment ajouter une analyse des sentiments à votre application LUIS afin d’analyser les énoncés de sentiments positifs, négatifs et neutres.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: a89755bcc0ed5ef8bee4ed00b99c73993a57bcb9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163020"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>Didacticiel : 9.  Ajouter l’analyse des sentiments
Dans ce tutoriel, vous allez créer une application montrant comment extraire le sentiment positif, négatif et neutre des énoncés.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre l’analyse des sentiments
> * Utiliser l’application LUIS pour le domaine des ressources humaines (RH) 
> * Ajouter l’analyse des sentiments
> * Entraîner et publier l’application
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS 

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [entité keyPhrase intégrée](luis-quickstart-intent-and-key-phrase.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle dans le site web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `sentiment`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="sentiment-analysis"></a>analyse de sentiments
L’analyse des sentiments permet de déterminer si l’énoncé d’un utilisateur est positif, négatif ou neutre. 

Les énoncés suivants présentent des exemples de sentiments :

|Sentiments|Score|Énoncé|
|:--|:--|:--|
|positif|0,91 |John W. Smith a bien réussi sa présentation à Paris.|
|positif|0,84 |jill-jones@mycompany.com a fait un travail fabuleux sur l’argumentaire de ventes Parker.|

L’analyse des sentiments est un paramètre d’application qui s’applique à chaque énoncé. Il est inutile de rechercher les mots indiquant un sentiment dans l’énoncé et de les étiqueter, car l’analyse des sentiments s’applique à l’énoncé entier. 

## <a name="add-employeefeedback-intent"></a>Ajouter l’intention RetoursEmployés 
Ajouter une nouvelle intention de recueillir des commentaires employés de la part des membres de la société. 

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Sélectionnez **Créer une intention**.

    [![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. Entrez le nom de la nouvelle intention `EmployeeFeedback`.

    ![Créez une nouvelle boîte de dialogue d’intention nommée RetoursEmployés](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Ajoutez plusieurs expressions qui indiquent qu’un employé fait bien les choses ou qu’un domaine a besoin d’améliorations :

    N’oubliez pas que dans cette application de ressources humaines, les employés sont définis dans l’entité de liste, `Employee`, par leur nom, leur e-mail, leur numéro d’extension de téléphone, leur numéro de téléphone mobile et leur numéro de sécurité sociale (USA). 

    |Énoncés|
    |--|
    |425-555-1212 a fait un bon travail pour accueillir un collaborateur suite à son congé maternité|
    |234-56-7891 a su réconforter un collègue en période de deuil.|
    |jill-jones@mycompany.com ne disposait pas de toutes les factures requises pour les tâches administratives.|
    |john.w.smith@mycompany.com a rendu les formulaires requis un mois trop tard avec aucune signature|
    |x23456 n’a pas pu se rendre à une réunion marketing hors site importante.|
    |x12345 a manqué la réunion des revues de juin.|
    |Jill Jones a parfaitement rôdé son argumentaire de vente à Harvard|
    |John W. Smith a bien réussi sa présentation à Stanford|

    [ ![Capture d’écran de l’application LUIS avec exemples d’énoncés dans l’intention de RetourEmployés](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurer l’application pour inclure l’analyse des sentiments
Configurez l’analyse des sentiments sur la page **Publier**. 

1. Dans le volet de navigation supérieur droit, sélectionnez **Publier**.

    ![Capture d’écran de la page d’intention avec le bouton Publier développé ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Sélectionnez **Enable Sentiment Analysis** (Activer l’analyse des sentiments). 

## <a name="publish-app-to-endpoint"></a>Publier l’application vers un point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>Interroger le point de terminaison avec un énoncé

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Jill Jones work with the media team on the public portal was amazing`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `EmployeeFeedback` avec l’analyse des sentiments extraite.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

sentimentAnalysis est positif avec un score de 0,86. 

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, avec l’analyse des sentiments activée, a identifié une intention de type requête en langage naturel et retourné les données extraites contenant le sentiment global sous forme de score. 

À présent, votre chatbot possède suffisamment d’informations pour déterminer l’étape suivante de la conversation. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de sentiment de l’énoncé pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Réviser les énoncés de point de terminaison dans l’application pour les RH](luis-tutorial-review-endpoint-utterances.md) 

