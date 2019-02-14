---
title: Intentions et entités prédéfinies
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez ajouter des intentions et des entités prédéfinies à une application pour obtenir rapidement une prédiction des intentions et l’extraction de données. Vous n’avez pas besoin d’étiqueter les énoncés avec des entités prédéfinies. L’entité est détectée automatiquement.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 346e7ac9a76f4ce4a87565ffc77032b095b2975c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877998"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Tutoriel : Identifier les intentions et entités courantes

Dans ce tutoriel, vous allez ajouter des intentions et des entités prédéfinies à une application de Ressources humaines pour obtenir rapidement une prédiction des intentions et l’extraction de données. Vous n’avez pas besoin de marquer les énoncés avec des entités prédéfinies, car l’entité est détectée automatiquement.

Les modèles prédéfinis (domaines, intentions et entités) vous aident à créer votre modèle rapidement.

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer une application
> * Ajouter des intentions prédéfinies 
> * Ajouter des entités prédéfinies 
> * Former 
> * Publish 
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Ajouter des intentions prédéfinies pour aider à déterminer les intentions utilisateurs courantes

LUIS fournit plusieurs intentions prédéfinies pour aider avec des intentions utilisateurs courantes.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Sélectionnez **Ajouter une intention de domaine prédéfinie**. 

1. Recherchez `Utilities`. 

    [![Capture d’écran de la boîte de dialogue Intentions prédéfinies avec Utilitaires dans la zone de recherche](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Sélectionnez les intentions suivantes, puis sélectionnez **Terminé** : 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    Ces intentions sont utiles pour déterminer où se trouve l’utilisateur dans la conversation et ce qu’il demande à faire. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Ajouter des entités prédéfinies pour faciliter l’extraction de types de données courants

LUIS fournit plusieurs entités prédéfinies pour l’extraction de données courantes. 

1. Dans le menu de navigation de gauche, sélectionnez **Entités**.

1. Sélectionnez le bouton **Ajouter une entité prédéfinie**.

1. Sélectionnez les entités suivantes dans la liste des entités prédéfinies, puis sélectionnez **Terminé** :

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    Ces entités vous permettent d’ajouter la reconnaissance de nom et de lieu à votre application cliente.

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None » 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les modifications apportées à l’intention puissent être testées 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Allez à la fin de l’URL dans la barre d’adresse du navigateur et entrez `I want to cancel my trip to Seattle to see Bob Smith`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **requête**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Le résultat a prédit l’intention Utilities.Cancel avec un score de confiance de 80 % et a extrait les données relatives aux noms de la ville et de la personne. 


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

Découvrez plus en détail les modèles prédéfinis :

* [Domaines prédéfinis](luis-reference-prebuilt-domains.md) : domaines courants qui laissent moins de place à la création d’applications LUIS globale
* Intentions prédéfinies : intentions individuelles des domaines courants. Vous pouvez ajouter des intentions individuellement au lieu d’ajouter le domaine entier.
* [Entités prédéfinies](luis-prebuilt-entities.md) : types de données courants utiles pour la plupart des applications LUIS.

Découvrez plus en détail l’utilisation de votre application LUIS :

* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Étapes suivantes

En ajoutant des intentions et des entités prédéfinies, l’application cliente peut déterminer des intentions utilisateur courantes et extraire des types de données communs.  

> [!div class="nextstepaction"]
> [Ajouter une entité de type expression régulière à l’application](luis-quickstart-intents-regex-entity.md)

