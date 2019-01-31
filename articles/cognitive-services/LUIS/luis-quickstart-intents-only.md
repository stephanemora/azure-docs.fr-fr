---
title: Prédire les intentions
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application personnalisée qui prédit l’intention d’un utilisateur. Cette application est le type d’application LUIS le plus simple, car elle n’extrait pas divers éléments de données du texte de l’énoncé tels que les adresses e-mail ou les dates.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 6d663dfe90bb8178b2c66b21a58d59618bac30a4
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223950"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Tutoriel : Générer une application LUIS pour déterminer les intentions d’un utilisateur

Dans ce tutoriel, vous créez une application Ressources humaines (RH) personnalisée qui prédit l’intention d’un utilisateur selon l’énoncé (texte). 

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer une application 
> * Créer des intentions
> * Ajouter des exemples d’énoncés
> * Entraîner une application
> * Publier une application
> * Reconnaître une intention à partir d’un point de terminaison


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Intentions utilisateur en tant qu’intentions

L’objet de l’application est de déterminer l’intention véhiculée par un texte de conversation en langage naturel : 

`Are there any new positions in the Seattle office?`

Ces intentions sont classées dans la catégorie **intentions**. 

Cette application a quelques intentions. 

|Intention|Objectif|
|--|--|
|ApplyForJob|Détermine si un utilisateur postule à un poste.|
|GetJobInformation|Détermine si un utilisateur recherche des informations sur les postes en général ou sur un poste spécifique.|
|Aucun|Détermine si l’utilisateur demande quelque chose auquel l’application n’est pas censée répondre. Cette intention est fournie dans le cadre de la création de l’application et elle ne peut pas être supprimée. |

## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Créer une intention pour des informations sur les postes

1. Sélectionnez **Créer une intention**. Entrez le nom de la nouvelle intention `GetJobInformation`. Cette intention est prédite quand un utilisateur demande des informations sur les postes libres dans l’entreprise. 

    ![Capture d’écran de la boîte de dialogue Nouvelle intention LUIS (Language Understanding)](media/luis-quickstart-intents-only/create-intent.png "Capture d’écran de la boîte de dialogue Nouvelle intention LUIS (Language Understanding)")

1. Sélectionnez **Terminé**.

2. Ajoutez plusieurs exemples d’énoncés à cette intention qu’un utilisateur est selon vous susceptible de demander :

    | Exemples d’énoncés|
    |--|
    |Nouveaux emplois publiés aujourd’hui ?|
    |Y a-t-il des postes à pourvoir au siège de Seattle ?|
    |Existe-t-il des postes pour des travailleurs distants ou en télétravail ouverts aux ingénieurs ?|
    |Existe-t-il des postes à pourvoir en bases de données ?|
    |Je recherche un poste en cotravail au bureau de Tampa.|
    |Y a-t-il un stage au bureau de San Francisco ?|
    |Y a-t-il un travail à temps partiel pour les personnes inscrites à l’université ?|
    |Recherche un nouveau poste avec des responsabilités en comptabilité|
    |Recherche un travail à New York City pour des personnes bilingues.|
    |Recherche un nouveau poste avec des responsabilités en comptabilité.|
    |Nouveaux emplois ?|
    |Me montrer tous les postes pour ingénieurs qui ont été ajoutés au cours des 2 derniers jours.|
    |Postes publiés aujourd’hui ?|
    |Quels sont les postes dans la comptabilité ouverts au bureau de Londres ?|
    |Quels postes sont disponibles pour des ingénieurs confirmés ?|
    |Où se trouve la liste de travail|

    [![Capture d’écran d'une saisie de nouveaux énoncés pour une intention MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Capture d’écran d'une saisie de nouveaux énoncés pour une intention MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    En fournissant des _exemples d’énoncés_, vous entraînez LUIS à déterminer les types d’énoncés à prédire pour cette intention. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None » 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Entraîner l’application avant un test ou la publication

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publier l’application pour faire des requêtes à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Obtenir une prédiction d’intention à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Allez à la fin de l’URL dans la barre d’adresses, puis entrez `I'm looking for a job with Natural Language Processing`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **requête**. Cet énoncé diffère de tous les exemples d’énoncés. Il constitue un bon test et doit retourner l’intention `GetJobInformation` en tant qu’intention avec le score le plus élevé. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Le paramètre de chaîne de requête `verbose=true` signifie qu’il faut inclure **toutes les intentions** dans les résultats de requête de l’application. Le tableau d’entités est vide, car cette application n’a pas d’entités. 

    Le résultat JSON identifie l’intention avec le score le plus élevé en tant que propriété **`topScoringIntent`**. Tous les scores sont compris entre 1 et 0, le meilleur score étant proche de 1. 

## <a name="create-intent-for-job-applications"></a>Créer une intention pour des candidatures à des postes

Revenez au portail LUIS et créez une intention pour déterminer si l’énoncé de l’utilisateur concerne la candidature à un poste.

1. Sélectionnez **Créer** dans le menu en haut à droite pour revenir à la création d’application.

1. Sélectionnez **Intentions** dans le menu de gauche pour accéder à la liste des intentions.

1. Sélectionnez **Créer une intention** et saisissez le nom `ApplyForJob`. 

    ![Boîte de dialogue LUIS pour la création d’une intention](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Ajoutez plusieurs énoncés à cette intention qu’un utilisateur est susceptible de demander selon vous :

    | Exemples d’énoncés|
    |--|
    |Remplissez l’application pour le poste 123456|
    |Voici mon c.v. pour le poste 654234|
    |Voici mon curriculum vitae pour le poste de réceptionniste à temps partiel.|
    |Je pose ma candidature au poste de graphiste avec ces documents.|
    |Je pose ma candidature pour le stage d’été à l’université au département Recherche et développement à San Diego|
    |Je voudrais envoyer mon CV pour le poste temporaire à la cafétéria.|
    |J’envoie mon CV pour la nouvelle équipe Autocar à Columbus, OHIO|
    |Je souhaite postuler pour le nouveau poste de comptable.|
    |Les documents pour le stage en comptabilité pour le poste 456789 se trouvent ici|
    |Emploi 567890 et mes documents|
    |Mes documents pour le stage de comptabilité à Tulsa sont joints.|
    |Mes documents pour le poste de livreur pendant la période des vacances|
    |Envoyez mon CV pour le nouveau poste en comptabilité à Seattle|
    |Envoyer CV pour le poste d’ingénieur|
    |Voici mon c.v. pour le poste 234123 à Tampa.|

    [![Capture d’écran d'une saisie de nouveaux énoncés pour une intention ApplyForJob](media/luis-quickstart-intents-only/utterance-applyforjob.png "Capture d’écran d'une saisie de nouveaux énoncés pour une intention ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    L’intention étiquetée est soulignée en rouge car LUIS n’est pas sûr de son exactitude. L’apprentissage de l’application indique à LUIS que les énoncés correspondent à l’intention. 

## <a name="train-again"></a>Réentraîner

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Republier

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Réobtenir les prédictions d’intention

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Dans la nouvelle fenêtre du navigateur, saisissez `Can I submit my resume for job 235986` à la fin de l’URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Les résultats incluent la nouvelle intention **ApplyForJob**, ainsi que les intentions existantes. 

## <a name="client-application-next-steps"></a>Étapes suivantes de l’application cliente

Une fois que LUIS a retourné la réponse JSON, il en a fini avec cette demande. LUIS ne fournit pas de réponses aux énoncés de l’utilisateur ; il identifie uniquement le type d’informations demandé dans un langage naturel. Le suivi de la conversation est fourni par l’application cliente telle qu’un bot Azure. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Types d’entités](luis-concept-entity-types.md)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé l’application Ressources humaines (RH), créé 2 intentions, ajouté des exemples d’énoncés pour chaque intention, ajouté des exemples d’énoncés à l’intention None, puis effectué un entraînement, une publication et un test au niveau du point de terminaison. Ce sont les étapes de base de la génération d’un modèle LUIS. 

Poursuivez avec cette application, [en ajoutant une entité simple et une liste d’expressions](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Ajouter des intentions et des entités prédéfinies à cette application](luis-tutorial-prebuilt-intents-entities.md)
