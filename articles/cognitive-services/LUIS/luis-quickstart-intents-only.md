---
title: Prédire les intentions
titleSuffix: Azure Cognitive Services
description: Créez une application personnalisée qui prédit l’intention de l’utilisateur. Cette application est le type d’application LUIS le plus simple, car elle n’extrait pas divers éléments de données du texte de l’énoncé tels que les adresses e-mail ou les dates.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b1a9718fdf7222dae06f7fe9b3a0f14b50293c08
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097792"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>Tutoriel 1 : Générer une application personnalisée pour déterminer les intentions de l’utilisateur

Dans ce tutoriel, vous créez une application Ressources humaines (RH) personnalisée qui prédit l’intention d’un utilisateur selon l’énoncé (texte). Une fois fini, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

L’objet de l’application consiste à déterminer l’intention véhiculée par un texte de conversation en langage naturel. Ces intentions sont classées dans la catégorie **intentions**. Cette application a quelques intentions. La première intention, **`GetJobInformation`**, identifie le moment où l’utilisateur veut des informations sur les emplois disponibles dans une entreprise. La deuxième intention, **`None`**, est utilisée pour tous les énoncés de l’utilisateur qui sont en dehors du _domaine_ (étendue) de cette application. Ultérieurement, une troisième intention, **`ApplyForJob`**, est ajoutée pour tous les énoncés sur la candidature à un poste de travail. Cette troisième intention est différente de `GetJobInformation`, car les informations sur le travail doivent être déjà connues quand quelqu’un postule pour celui-ci. Toutefois, suivant le choix des mots, la détermination de l’intention peut être difficile, car toutes deux concernent un poste de travail.

Une fois que LUIS a retourné la réponse JSON, il en a fini avec cette demande. LUIS ne fournit pas de réponses aux énoncés de l’utilisateur ; il identifie uniquement le type d’informations demandé dans un langage naturel. 

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer une application 
> * Créer des intentions
> * Ajouter des exemples d’énoncés
> * Entraîner une application
> * Publier une application
> * Reconnaître une intention à partir d’un point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Créer une application

1. Connectez-vous au portail LUIS avec l’URL de [https://www.luis.ai](https://www.luis.ai). 

2. Sélectionnez **Créer une application**.  

    [![Capture d’écran de la page Mes applications LUIS (Language Understanding)](media/luis-quickstart-intents-only/app-list.png "Capture d’écran de la page Mes applications LUIS (Language Understanding)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Dans la boîte de dialogue contextuelle, entrez le nom `HumanResources` et conservez la culture par défaut, **Anglais**. Ne renseignez pas la description.

    ![Créer une applications HumanResources LUIS](./media/luis-quickstart-intents-only/create-app.png)

    Ensuite, l’application affiche la page **Intents** (Intentions) avec l’intention **None**.

## <a name="getjobinformation-intent"></a>Intention GetJobInformation

1. Sélectionnez **Créer une intention**. Entrez le nom de la nouvelle intention `GetJobInformation`. Cette intention est prédite à chaque fois qu’un utilisateur cherche des informations sur les postes libres dans l’entreprise.

    ![Capture d’écran de la boîte de dialogue Nouvelle intention LUIS (Language Understanding)](media/luis-quickstart-intents-only/create-intent.png "Capture d’écran de la boîte de dialogue Nouvelle intention LUIS (Language Understanding)")

2. En fournissant des _exemples d’énoncés_, vous entraînez LUIS à déterminer les types d’énoncés à prédire pour cette intention. Ajoutez plusieurs exemples d’énoncés à cette intention qu’un utilisateur est susceptible de demander selon vous :

    | Exemples d’énoncés|
    |--|
    |Nouveaux emplois publiés aujourd’hui ?|
    |Quels postes sont disponibles pour des ingénieurs confirmés ?|
    |Existe-t-il des postes à pourvoir en bases de données ?|
    |Recherche un nouveau poste avec des responsabilités en comptabilité|
    |Où se trouve la liste de travail|
    |Nouveaux emplois ?|
    |Y a-t-il des postes à pourvoir au siège de Seattle ?|

    [![Capture d’écran d'une saisie de nouveaux énoncés pour une intention MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Capture d’écran d'une saisie de nouveaux énoncés pour une intention MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>Intention None 
L’application cliente a besoin de savoir si un énoncé est en dehors du domaine de l’objet de l’application. Si LUIS retourne l’intention **None** pour un énoncé, votre application cliente peut demander si l’utilisateur souhaite mettre fin à la conversation. L’application cliente peut également donner d’autres directions pour poursuivre la conversation si l’utilisateur ne souhaite pas y mettre un terme. 

Ces exemples d’énoncés, en dehors du domaine de l’objet, sont regroupés dans l’intention **None**. Elle ne doit pas être vide. 

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions).

2. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application Ressources humaines. Si l’application concerne la publication d’offre d’emploi, voici quelques exemples d’énoncés pour l’intention **None** :

    | Exemples d’énoncés|
    |--|
    |Les chiens qui aboient sont agaçants|
    |Commande une pizza pour moi|
    |Pingouins dans l’océan|


## <a name="train"></a>Former 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Reconnaître l’intention

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Allez à la fin de l’URL dans la barre d’adresses, puis entrez `I'm looking for a job with Natural Language Processing`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **requête**. Cet énoncé diffère de tous les exemples d’énoncés. Il constitue un bon test et doit retourner l’intention `GetJobInformation` en tant qu’intention avec le score le plus élevé. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    Les résultats incluent **toutes les intentions** dans l’application, actuellement au nombre de 2. Le tableau d’entités est vide, car cette application n’a pas d’entités. 

    Le résultat JSON identifie l’intention avec le score le plus élevé en tant que propriété **`topScoringIntent`**. Tous les scores sont compris entre 1 et 0, le meilleur score étant proche de 1. 

## <a name="applyforjob-intent"></a>Intention ApplyForJob
Revenez au site web LUIS et créez une intention pour déterminer si l’énoncé de l’utilisateur concerne la candidature à un poste de travail.

1. Sélectionnez **Créer** dans le menu en haut à droite pour revenir à la création d’application.

2. Dans le menu gauche, sélectionnez **Intents** (Intentions).

3. Sélectionnez **Créer une intention** et saisissez le nom `ApplyForJob`. 

    ![Boîte de dialogue LUIS pour la création d’une intention](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Ajoutez plusieurs énoncés à cette intention qu’un utilisateur est susceptible de demander selon vous :

    | Exemples d’énoncés|
    |--|
    |Je souhaite postuler pour le nouveau poste de comptable.|
    |Remplissez l’application pour le poste 123456|
    |Envoyer CV pour le poste d’ingénieur|
    |Voici mon c.v. pour le poste 654234|
    |Emploi 567890 et mes documents|

    [![Capture d’écran d'une saisie de nouveaux énoncés pour une intention ApplyForJob](media/luis-quickstart-intents-only/utterance-applyforjob.png "Capture d’écran d'une saisie de nouveaux énoncés pour une intention ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    L’intention étiquetée est soulignée en rouge car LUIS n’est pas sûr de son exactitude. L’apprentissage de l’application indique à LUIS que les énoncés correspondent à l’intention. 

## <a name="train-again"></a>Réentraîner

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Republier

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Reconnaître l’intention de nouveau

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Dans la nouvelle fenêtre du navigateur, saisissez `Can I submit my resume for job 235986` à la fin de l’URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    Les résultats incluent la nouvelle intention **ApplyForJob**, ainsi que les intentions existantes. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé l’application Ressources humaines (RH), créé 2 intentions, ajouté des exemples d’énoncés pour chaque intention, ajouté des exemples d’énoncés à l’intention None, puis effectué un entraînement, une publication et un test au niveau du point de terminaison. Ce sont les étapes de base de la génération d’un modèle LUIS. 

> [!div class="nextstepaction"]
> [Ajouter des intentions et des entités prédéfinies à cette application](luis-tutorial-prebuilt-intents-entities.md)
