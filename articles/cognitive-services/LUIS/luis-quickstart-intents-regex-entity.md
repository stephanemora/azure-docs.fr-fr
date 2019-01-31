---
title: Entité Expression régulière
titleSuffix: Azure Cognitive Services
description: Extrayez des données mises en forme de façon homogène depuis un énoncé à l’aide de l’entité Expression régulière.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 7ce8ba5d35d4c8f8def7b87e96f102c45d39a5d7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228081"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutoriel : Obtenir des données au format approprié à partir de l’énoncé
Dans ce tutoriel, vous créez une application pour extraire des données mises en forme de façon homogène à partir d’un énoncé avec l’entité **Expression régulière**.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer une application 
> * Ajouter une intention
> * Ajouter une entité d’expression régulière 
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entité d’expression régulière

Dans cette application, l’utilisation de l’entité d’expression régulière consiste à extraire les numéros de formulaire de Ressources humaines (RH) au format approprié à partir d’un énoncé. Bien que l’intention de l’énoncé soit toujours déterminée avec l’apprentissage automatique, ce type d’entité spécifique n’est pas issu du machine learning. 

**Les exemples d’énoncés sont les suivants :**

|Exemples d’énoncés|
|--|
|Où se trouve HRF-123456 ?|
|Qui a créé HRF-123234 ?|
|HRF-456098 est-il publié en français ?|
|HRF-456098|
|Date de HRF-456098 ?|
 
Une expression régulière est un choix approprié pour ce type de données quand :

* les données sont bien formées.


## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Créer une intention pour trouver un formulaire

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Sélectionnez **Créer une intention**. 

1. Entrez `FindForm` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**. 

    ![Capture d’écran de la boîte de dialogue de création d’une intention avec Utilities (Utilitaires) dans la zone de recherche](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Ajoutez des exemples d’énoncés à l’intention.

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

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Utiliser l’entité d’expression régulière pour les données au format approprié
L’entité d’expression régulière correspondant au numéro du formulaire est `hrf-[0-9]{6}`. Cette expression régulière correspond aux caractères littéraux `hrf-`, mais ignore les variantes de casse et de culture. Elle correspond aux chiffres 0 à 9, pour 6 chiffres exactement.

HRF signifie `human resources form`.

LUIS segmente l’énoncé en unités lexicales quand il est ajouté à une intention. La segmentation du texte de ces énoncés en unités lexicales ajoute des espaces avant et après le trait d’union, `Where is HRF - 123456?` L’expression régulière est appliquée à l’énoncé à l’état brut, avant que ce dernier soit segmenté en unité lexicales. Comme elle est appliquée à l’état _brut_, l’expression régulière n’a pas besoin de tenir compte des limites de mot. 

Créez une entité d’expression régulière pour indiquer à LUIS ce qu’est un format de numéro HRF en effectuant les étapes suivantes :

1. Dans le panneau gauche, sélectionnez **Entités**.

1. Cliquez sur le bouton **Create new entity** (Créer une entité) sur la Page d’entités. 

1. Dans la boîte de dialogue contextuelle, entrez le nouveau nom d’entité `HRF-number`, sélectionnez **RegEx** comme type d’entité, entrez `hrf-[0-9]{6}` comme valeur **d’expression régulière**, puis sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue contextuelle de définition des nouvelles propriétés de l’entité](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Sélectionnez **Intents** (Intentions) dans le menu de gauche, puis l’intention **FindForm** pour voir l’expression régulière étiquetée dans les énoncés. 

    [![Capture d’écran d’étiquetage d’un énoncé avec une entité existante et un modèle d’expression régulière](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Dans la mesure où l’entité n’est pas une entité issue de l’apprentissage automatique, l’entité est appliquée aux énoncés et affichée sur le site web de LUIS dès sa création.

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None » 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Entraîner l’application avant un test ou la publication

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publier l’application vers la requête à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `When were HRF-123456 and hrf-234567 published in the last year?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `FindForm` avec les deux numéros de formulaire `HRF-123456` et `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
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
        }
      ]
    }
    ```

    En utilisant une entité d’expression régulière, LUIS extrait les données nommées, ce qui est plus utile, sur le plan de la programmation, à l’application cliente qui reçoit la réponse JSON.


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* Concepts d’entité [d’expression régulière](luis-concept-entity-types.md#regular-expression-entity)
* [Comment effectuer l’apprentissage](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une intention, ajouté des exemples d’énoncés, puis créé une entité d’expression régulière pour extraire des données bien formées à partir des énoncés. Une fois l’application entraînée et publiée, une requête au point de terminaison a identifié l’intention et retourné les données extraites.

> [!div class="nextstepaction"]
> [En savoir plus sur l’entité de liste](luis-quickstart-intent-and-list-entity.md)

