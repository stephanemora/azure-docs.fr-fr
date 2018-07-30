---
title: Didacticiel de création d’une application LUIS pour obtenir des données correspondant à une expression régulière - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment créer une application LUIS simple utilisant une entité d’expression régulière et des intentions pour extraire les données.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 99f796bf26df755ca938c3023057e2e9de1706a1
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238333"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>Tutoriel : 3. Ajouter une entité d’expression régulière
Dans ce tutoriel, vous créez une application qui montre comment extraire des données mises en forme de façon homogène à partir d’un énoncé avec l’entité **Expression régulière**.


<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités d’expression régulière 
> * Utiliser une application LUIS pour un domaine de ressources humaines (RH) avec l’intention FindForm
> * Ajouter une entité d’expression régulière pour extraire un numéro de formulaire d’un énoncé
> * Effectuer l’apprentissage et publier l’application
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

Pour cet article, vous devez disposer d’un compte [LUIS](luis-reference-regions.md#luis-website) gratuit afin de créer votre application LUIS.

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel sur les [entités intégrées](luis-tutorial-prebuilt-intents-entities.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle application dans le site web [LUIS](luis-reference-regions.md#luis-website), à partir du dépôt Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `regex`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 


## <a name="purpose-of-the-regular-expression-entity"></a>Objectif de l’entité d’expression régulière
L’objectif d’une entité est d’obtenir les données importantes contenues dans l’énoncé. L’application utilise l’entité d’expression régulière pour extraire les numéros de formulaire de Ressources humaines (RH) à partir d’un énoncé. Ces informations ne sont pas issues de l’apprentissage automatique. 

Les exemples simples d’énoncés d’utilisateurs sont les suivants :

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Les versions abrégées ou argotiques d’énoncés sont les suivants :

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
L’entité d’expression régulière correspondant au numéro du formulaire est `hrf-[0-9]{6}`. Cette expression régulière correspond aux caractères littéraux `hrf -`, mais ignore les variantes de casse et de culture. Elle correspond aux chiffres 0 à 9, pour 6 chiffres exactement.

HRF désigne le formulaire de ressources humaines.

### <a name="tokenization-with-hyphens"></a>Segmentation du texte en unités lexicales avec des traits d’union
LUIS segmente le texte de l’énoncé en unités lexicales lorsque l’énoncé est ajouté à une intention. La segmentation du texte de ces énoncés en unités lexicales ajoute des espaces avant et après le trait d’union, `Where is HRF - 123456?` L’expression régulière est appliquée à l’énoncé à l’état brut, avant que ce dernier soit segmenté en unité lexicales. Comme elle est appliquée à l’état _brut_, l’expression régulière n’a pas besoin de tenir compte des limites de mot. 


## <a name="add-findform-intent"></a>Ajouter une intention FindForm

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-quickstart-intents-regex-entity/first-image.png)](./media/luis-quickstart-intents-regex-entity/first-image.png#lightbox)

2. Sélectionnez **Create new intent** (Créer une intention). 

    [ ![Capture d’écran de la page Intents (Intentions) avec le bouton Create new intent (Créer une intention) mis en surbrillance](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png) ](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png#lightbox)

3. Entrez `FindForm` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**. 

    ![Capture d’écran de la boîte de dialogue de création d’une intention avec Utilities (Utilitaires) dans la zone de recherche](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Quelle est l’URL pour hrf-123456 ?|
    |Où se trouve hrf-345678 ?|
    |Quand a été effectuée la mise à jour de hrf-456098 ?|
    |John Smith a-t-il effectué la mise à jour de hrf-234639 la semaine dernière ?|
    |Combien y a-t-il de versions de hrf-345123 ?|
    |Qui a besoin d’autoriser le formulaire hrf-123456 ?|
    |Combien de personnes ont besoin de valider hrf-345678 ?|
    |Date de hrf-234123 ?|
    |Auteur de hrf-546234 ?|
    |Titre de hrf-456234 ?|

    [ ![Capture d’écran de la page d’intentions avec de nouveaux énoncés mis en surbrillance](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    L’application comporte une entité de nombre prédéfinie ajoutée à partir du didacticiel précédent, de sorte que chaque numéro de formulaire est étiqueté. Cela peut suffire pour votre application cliente, mais le numéro ne sera pas étiqueté avec le type de numéro. Création d’une entité avec un nom approprié permet à l’application cliente de traiter l’entité correctement lorsqu’elle est retournée à partir de LUIS.

## <a name="create-a-hrf-number-regular-expression-entity"></a>Créer une entité d’expression régulière de numéro HRF 
Créez une entité d’expression régulière pour indiquer à LUIS ce qu’est un format de numéro HRF en effectuant les étapes suivantes :

1. Dans le panneau gauche, sélectionnez **Entités**.

2. Cliquez sur le bouton **Create new entity** (Créer une entité) sur la Page d’entités. 

    [![Capture d’écran de la page d’entités avec le bouton Create new entity (Créer une entité) mis en surbrillance](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png)](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png#lightbox)

3. Dans la boîte de dialogue contextuelle, entrez le nouveau nom d’entité `HRF-number`, sélectionnez **RegEx** comme type d’entité, entrez `hrf-[0-9]{6}` comme expression régulière, puis sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue contextuelle de définition des nouvelles propriétés de l’entité](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Sélectionnez **Intents** (Intentions), puis l’intention **FindForm** pour voir l’expression régulière étiquetée dans les énoncés. 

    [![Capture d’écran d’étiquetage d’un énoncé avec une entité existante et un modèle d’expression régulière](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Dans la mesure où l’entité n’est pas une entité issue de l’apprentissage automatique, l’étiquette est appliquée aux énoncés et affichée sur le site web de LUIS dès sa création.

## <a name="train-the-luis-app"></a>Effectuer l’apprentissage de l’application LUIS
Une entité d’expression régulière ne nécessite pas de formation, mais la nouvelle intention et les nouveaux énoncés nécessitent un apprentissage. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Image du bouton d’apprentissage](./media/luis-quickstart-intents-regex-entity/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Image de la barre de notification de réussite](./media/luis-quickstart-intents-regex-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

    ![Capture d’écran de FindKnowledgeBase avec le bouton Publier e la barre de navigation supérieure mis en surbrillance](./media/luis-quickstart-intents-regex-entity/publish-button.png)

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    ![Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance](./media/luis-quickstart-intents-regex-entity/publish-to-production.png)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    ![Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance](./media/luis-quickstart-intents-regex-entity/publish-select-endpoint.png)

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `When were HRF-123456 and hrf-234567 published in the last year?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `FindForm` avec les deux numéros de formulaire `HRF-123456` et `hrf-234567`.

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Les numéros dans l’énoncé sont retournés deux fois, une fois en tant que nouvelle entité `hrf-number`et une fois en tant qu’entité prédéfinie, `number`. Un énoncé peut comporter plusieurs entités et plusieurs occurrences du même type d’entité, comme le montre cet exemple. En utilisant une entité d’expression régulière, LUIS extrait les données nommées, ce qui est plus utile, sur le plan de la programmation, à l’application cliente qui reçoit la réponse JSON.

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application a identifié l’intention et retourné les données extraites. 

Votre chatbot a maintenant suffisamment d’informations pour déterminer l’action principale, `FindForm`, et les numéros de formulaire contenus dans la recherche. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les numéros de formulaire et rechercher une API tierce. LUIS n’effectue pas ce travail. LUIS détermine uniquement ce qu’est l’intention de l’utilisateur et extrait des données sur cette intention. 

## <a name="clean-up-resources"></a>Supprimer les ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Sélectionnez **Mes applications** dans le menu en haut à gauche. Sélectionnez les points de suspension (***...***) à droite du nom de l’application dans la liste des applications, sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’entité de liste](luis-quickstart-intent-and-list-entity.md)

