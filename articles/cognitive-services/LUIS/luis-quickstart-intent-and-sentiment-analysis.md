---
title: analyse de sentiments
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application montrant comment obtenir le sentiment positif, négatif et neutre des énoncés. Le sentiment est déterminé à partir de l’énoncé entier.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 3315af0898cb3b18af0334a433a94242b056a8bd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236197"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Didacticiel :  Obtenir le sentiment d’un énoncé

Dans ce tutoriel, vous allez créer une application montrant comment déterminer le sentiment positif, négatif et neutre des énoncés. Le sentiment est déterminé à partir de l’énoncé entier.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer une application
> * Ajouter l’analyse des sentiments comme paramètre de publication
> * Entraîner une application
> * Publier une application
> * Reconnaître le sentiment de l’énoncé à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>L’analyse des sentiments est un paramètre de publication

Les énoncés suivants présentent des exemples de sentiments :

|Sentiments|Score|Énoncé|
|:--|:--|:--|
|positif|0,91 |John W. Smith a bien réussi sa présentation à Paris.|
|positif|0,84 |Les ingénieurs de Seattle ont fait un travail fabuleux sur l’argumentaire de ventes Parker.|

L’analyse des sentiments est un paramètre de publication qui s’applique à chaque énoncé. Vous ne devez pas rechercher les mots indiquant le sentiment dans l’énoncé et les marquer. 

S’agissant d’un paramètre de publication, vous ne le voyez pas sur les pages d’intentions ou d’entités. Vous pouvez le voir dans le volet [interactive test](luis-interactive-test.md#view-sentiment-results) (test interactif) ou quand vous effectuez un test au niveau de l’URL de point de terminaison. 


## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Ajouter l’entité prédéfinie PersonName 


1. Dans le menu de navigation de gauche, sélectionnez **Entités**.

1. Sélectionnez le bouton **Ajouter une entité prédéfinie**.

1. Sélectionnez l’entité suivante dans la liste des entités prédéfinies, puis sélectionnez **Terminé** :

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Créer une intention pour déterminer le feedback des employés

Ajouter une nouvelle intention de recueillir des commentaires employés de la part des membres de la société. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Sélectionnez **Créer une intention**.

3. Entrez le nom de la nouvelle intention `EmployeeFeedback`.

    ![Créez une nouvelle boîte de dialogue d’intention nommée RetoursEmployés](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Ajoutez plusieurs expressions qui indiquent qu’un employé fait bien les choses ou qu’un domaine a besoin d’améliorations :

    |Énoncés|
    |--|
    |John Smith a fait un bon travail pour accueillir une collaboratrice à son retour de congé maternité|
    |Jill Jones a su réconforter un collègue en période de deuil.|
    |Bob Barnes ne disposait pas de toutes les factures nécessaires pour les tâches d’administration.|
    |Todd Thomas a rendu les formulaires nécessaires avec un mois de retard, sans aucune signature|
    |Katherine Kelly n’a pas pu se rendre à une réunion marketing hors site importante.|
    |Denise Dillard a manqué la réunion des revues de juin.|
    |Mark Mathews a parfaitement rôdé son argumentaire de vente à Harvard|
    |Walter Williams a bien réussi sa présentation à Stanford|

    Sélectionnez **Afficher les options**, puis **Afficher les valeurs de l’entité** pour voir les noms.

    [![Capture d’écran de l’application LUIS avec exemples d’énoncés dans l’intention EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None » 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les modifications apportées à l’intention puissent être testées 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurer l’application pour inclure l’analyse des sentiments

1. Sélectionnez **Gérer** dans le volet de navigation droit supérieur, puis sélectionnez **Paramètres de publication** dans le menu de gauche.

1. Sélectionnez **Analyse des sentiments** pour activer ce paramètre. 

    ![Activer l'Analyse des sentiments comme paramètre de publication](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obtenir le sentiment d’un énoncé à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Jill Jones work with the media team on the public portal was amazing`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `EmployeeFeedback` avec l’analyse des sentiments extraite.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
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

    sentimentAnalysis est positif avec un score de 86 %. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* L’analyse des sentiments est fournie par la fonctionnalité [Analyse de texte](../Text-Analytics/index.yml) de Cognitive Services. La fonctionnalité est limitée aux [langues prises en charge](luis-language-support.md##languages-supported) par Analyse de texte.
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Étapes suivantes
Ce tutoriel ajoute l’analyse des sentiments en tant que paramètre de publication pour extraire les valeurs de sentiment de l’énoncé dans sa globalité.

> [!div class="nextstepaction"] 
> [Réviser les énoncés de point de terminaison dans l’application pour les RH](luis-tutorial-review-endpoint-utterances.md) 

