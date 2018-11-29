---
title: 'Tutoriel 9 : Analyse des sentiments notamment des sentiments positifs, négatifs et neutres dans LUIS'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application montrant comment extraire le sentiment positif, négatif et neutre des énoncés. Le sentiment est déterminé à partir de l’énoncé entier.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 60c4b280033e110f6b8b2a3ce720934e118c8479
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424848"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Tutoriel 9 : Extraire le sentiment de l’énoncé global
Dans ce tutoriel, vous allez créer une application montrant comment extraire le sentiment positif, négatif et neutre des énoncés. Le sentiment est déterminé à partir de l’énoncé entier.

L’analyse des sentiments permet de déterminer si l’énoncé d’un utilisateur est positif, négatif ou neutre. 

Les énoncés suivants présentent des exemples de sentiments :

|Sentiments|Score|Énoncé|
|:--|:--|:--|
|positif|0,91 |John W. Smith a bien réussi sa présentation à Paris.|
|positif|0,84 |jill-jones@mycompany.com a fait un travail fabuleux sur l’argumentaire de ventes Parker.|

L’analyse des sentiments est un paramètre de publication qui s’applique à chaque énoncé. Il est inutile de rechercher les mots indiquant un sentiment dans l’énoncé et de les étiqueter, car l’analyse des sentiments s’applique à l’énoncé entier. 

S’agissant d’un paramètre de publication, vous ne le voyez pas sur les pages d’intentions ou d’entités. Vous pouvez le voir dans le volet [interactive test](luis-interactive-test.md#view-sentiment-results) (test interactif) ou quand vous effectuez un test au niveau de l’URL de point de terminaison. 

**Ce tutoriel vous montre comment effectuer les opérations suivantes :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante 
> * Ajouter l’analyse des sentiments comme paramètre de publication
> * Former
> * Publish
> * Reconnaître le sentiment de l’énoncé à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante

Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `sentiment`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="employeefeedback-intent"></a>Intention RetoursEmployés 
Ajouter une nouvelle intention de recueillir des commentaires employés de la part des membres de la société. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Sélectionnez **Créer une intention**.

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

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurer l’application pour inclure l’analyse des sentiments
1. Sélectionnez **Gérer** dans le volet de navigation droit supérieur, puis sélectionnez **Paramètres de publication** dans le menu de gauche.

2. Activez **Analyse des sentiments** pour activer ce paramètre. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Reconnaître le sentiment de l’énoncé à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Jill Jones work with the media team on the public portal was amazing`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `EmployeeFeedback` avec l’analyse des sentiments extraite.
    
    ```JSON
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

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes
Ce tutoriel ajoute l’analyse des sentiments en tant que paramètre de publication pour extraire les valeurs de sentiment de l’énoncé dans sa globalité.

> [!div class="nextstepaction"] 
> [Réviser les énoncés de point de terminaison dans l’application pour les RH](luis-tutorial-review-endpoint-utterances.md) 

