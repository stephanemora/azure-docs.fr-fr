---
title: Créer une application simple avec deux intentions - Azure | Microsoft Docs
description: Dans ce guide de démarrage rapide, apprenez à créer une application LUIS simple utilisant deux intentions et aucune entité pour identifier les énoncés de l’utilisateur.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: de295a93d395cee4c4dfbea4f2e7f7338036feb8
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494371"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Didacticiel : 1. Créer une application avec un domaine personnalisé
Dans ce didacticiel, créez une application qui montre comment utiliser des **intentions** pour déterminer l’_intention_ de l’utilisateur selon l’énoncé (texte) saisi dans l’application. Une fois fini, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

Cette application est le type le plus simple d’application LUIS, car elle n’extrait aucune donnée des énoncés. Elle détermine seulement l’intention de l’utilisateur selon l’énoncé.

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer une application pour un domaine des ressources humaines (RH) 
> * Ajouter l’intention GetJobInformation
> * Ajouter des énoncés exemple à l’intention GetJobInformation 
> * Effectuer l’apprentissage de l’application et la publier
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS
> * Ajouter l’intention ApplyForJob
> * Ajouter des énoncés exemple à l’intention ApplyForJob 
> * Effectuer l’apprentissage, publier, et interroger à nouveau le point de terminaison 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>Objet de l’application
Cette application a quelques intentions. La première intention, **`GetJobInformation`**, identifie le moment où l’utilisateur veut des informations sur les emplois disponibles dans une entreprise. La deuxième, **`None`**, identifie tous les autres types d’énoncé. Plus tard dans ce guide, une troisième intention, `ApplyForJob`, est ajoutée. 

## <a name="create-a-new-app"></a>Créer une application
1. Connectez-vous au site web [LUIS](luis-reference-regions.md#luis-website). Veillez à vous connecter à la [région](luis-reference-regions.md#publishing-regions) où vous avez besoin de points de terminaison LUIS publiés.

2. Sur le site web [LUIS](luis-reference-regions.md#luis-website), sélectionnez **Créer une application**.  

    [![](media/luis-quickstart-intents-only/app-list.png "Capture d’écran de la page Mes applications")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Dans la boîte de dialogue contextuelle, entrez le nom `HumanResources`. Cette application répond aux questions relatives au département des ressources humaines de votre entreprise. Ce type de département gère les problèmes relatifs à l’emploi, comme les postes à pourvoir dans l’entreprise.

    ![Nouvelle application LUIS](./media/luis-quickstart-intents-only/create-app.png)

4. Lorsque ce processus se termine, l’application affiche la page **Intents** (Intentions) avec l’intention **None**. 

## <a name="create-getjobinformation-intention"></a>Créer l’intention GetJobInformation
1. Sélectionnez **Create new intent** (Créer une intention). Entrez le nom de la nouvelle intention `GetJobInformation`. Cette intention est prédite à chaque fois qu’un utilisateur cherche des informations sur les postes libres dans votre entreprise.

    ![](media/luis-quickstart-intents-only/create-intent.png "Capture d’écran de la boîte de dialogue Nouvelle intention")

    En créant une intention, vous créez une catégorie d’informations que vous souhaitez identifier. Attribuer un nom à la catégorie permet à toute autre application utilisant les résultats de la requête LUIS d’utiliser ce nom de catégorie pour trouver une réponse appropriée. LUIS ne répond pas à ces questions, et identifie uniquement le type d’informations demandé dans un langage naturel. 

2. Ajoutez sept énoncés à cette intention qu’un utilisateur est susceptible de demander selon vous :

    | Exemples d’énoncés|
    |--|
    |Nouveaux emplois publiés aujourd’hui ?|
    |Quels postes sont disponibles pour des ingénieurs confirmés ?|
    |Existe-t-il des postes à pourvoir en bases de données ?|
    |Recherche un nouveau poste avec des responsabilités en comptabilité|
    |Où se trouve la liste de travail|
    |Nouveaux emplois ?|
    |Y a-t-il des postes à pourvoir au siège de Seattle ?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Capture d’écran d’une saisie de nouveaux énoncés pour une intention MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. Pour le moment, l’application LUIS ne dispose d’aucun énoncé pour l’intention **None**. Elle a besoin d’énoncés, sans quoi elle ne répond pas. Renseignez cette zone. Dans le panneau gauche, sélectionnez **Intents** (Intentions). 

4. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application. Si l’application concerne la publication d’offre d’emploi, voici quelques exemples d’énoncés appropriés pour l’intention **None** :

    | Exemples d’énoncés|
    |--|
    |Les chiens qui aboient sont agaçants|
    |Commande une pizza pour moi|
    |Pingouins dans l’océan|

    Dans votre application d’appel de LUIS, comme un chatbot, si LUIS retourne l’intention **None** pour un énoncé, votre robot peut demander si l’utilisateur souhaite mettre fin à la conversation. Le robot peut également donner d’autres directions pour poursuivre la conversation si l’utilisateur ne souhaite pas y mettre un terme. 

## <a name="train-and-publish-the-app"></a>Former et publier l’application

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publier l’application vers un point de terminaison

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Interroger le point de terminaison de l’intention GetJobInformation

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `I'm looking for a job with Natual Language Processing`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés de l’étape 4, c’est un bon test qui doit retourner l’intention `GetJobInformation` comme intention la mieux notée. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
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

## <a name="create-applyforjob-intention"></a>Créer l’intention ApplyForJob
Retournez à l’onglet du navigateur du site web LUIS et créez une intention pour la demande d’emploi.

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

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Capture d’écran d’une saisie de nouveaux énoncés pour une intention ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    L’intention étiquetée est soulignée en rouge car LUIS n’est pas sûr de son exactitude. L’apprentissage de l’application indique à LUIS que les énoncés correspondent à l’intention. 

    [Effectuez l’apprentissage et publier](#train-and-publish-the-app) à nouveau. 

## <a name="query-endpoint-for-applyforjob-intent"></a>Interroger le point de terminaison de l’intention ApplyForJob

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Dans la nouvelle fenêtre du navigateur, saisissez `Can I submit my resume for job 235986` à la fin de l’URL. 

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, avec quelques intentions seulement, a identifié une requête de langue naturelle de la même intention mais formulée différemment. 

Le résultat JSON identifie l’intention. Tous les scores sont compris entre 1 et 0, le meilleur score étant proche de 1. Le score des intentions `GetJobInformation` et `None` est plus proche de zéro. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application qui appelle, comme un chatbot, peut prendre le résultat topScoringIntent et trouver des informations (qui ne sont pas dans LUIS) pour répondre à la question ou clore la conversation. Il s’agit d’options de programmation du bot ou de l’application. LUIS n’effectue pas ce travail. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer les ressources

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des intentions et des entités prédéfinies à cette application](luis-tutorial-prebuilt-intents-entities.md)
