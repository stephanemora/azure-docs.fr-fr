---
title: 'Didacticiel : Prédire les intentions - LUIS'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application personnalisée qui prédit l’intention d’un utilisateur. Cette application est le type d’application LUIS le plus simple, car elle n’extrait pas divers éléments de données du texte de l’énoncé tels que les adresses e-mail ou les dates.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 4d096ee829a425af3763c212daf5049acccf9f19
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325916"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Didacticiel : Générer une application LUIS pour déterminer les intentions d’un utilisateur

Dans ce didacticiel, vous allez créer une application personnalisée qui prédit l’intention d’un utilisateur sur la base de l’énoncé (texte). 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer une application 
> * Créer des intentions
> * Ajouter des exemples d’énoncés
> * Entraîner une application
> * Publier une application
> * Obtenir une prédiction d’intention à partir du point de terminaison


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>Intentions utilisateur en tant qu’intentions

L’objet de l’application est de déterminer l’intention véhiculée par un texte de conversation en langage naturel : 

`I'd like to order a veggie pizza with a salad on the side.`

Ces intentions sont classées dans la catégorie **intentions**. 

|Intention|Objectif|
|--|--|
|`ModifyOrder`|Déterminer la commande de pizza de l’utilisateur.|
|`Greeting`|Commencez la conversation avec le bot.|
|`ConfirmOrder`|Confirmez la commande de pizza.|
|`None`|Déterminez si l’utilisateur demande une chose à laquelle l’application n’est pas censée répondre. Cette intention est fournie dans le cadre de la création de l’application et elle ne peut pas être supprimée. |

## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Créer une intention 

1. Sur le portail, dans la section **Générer**, sélectionnez **+ Créer**. Entrez le nom de l’intention (`OrderPizza`), puis sélectionnez **Terminé**.

    L’intention `OrderPizza` est prédite quand un utilisateur veut commander une pizza. 

1. Ajoutez plusieurs exemples d’énoncés à cette intention qu’un utilisateur est selon vous susceptible de demander :

    |Exemples d’énoncés `OrderPizza`|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Ajouter des exemples d’énoncés](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    En fournissant des _exemples d’énoncés_, vous entraînez LUIS à déterminer les types d’énoncés à prédire pour cette intention. 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>Créer les intentions restantes

1. Créez l’intention `Greeting` et ajoutez les exemples d’énoncés suivants. Ceci est l’intention pour déterminer si un utilisateur entame une nouvelle conversation pour commander une pizza.

    |Exemples d’énoncés `Greeting`|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Créez l’intention `Confirm` et ajoutez les exemples d’énoncés suivants. Ceci est l’intention pour déterminer si un utilisateur a fini de commander et accepte les détails de la commande. 

    |Exemples d’énoncés `Confirm`|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Exemples d’énoncés de l’intention None

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publier l’application 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>Obtenir une prédiction d’intention

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Allez à la fin de l’URL dans la barre d’adresses, puis entrez :

    `get a medium vegetarian pizza for delivery` 

    Ceci n’est pas exactement la même chose qu’un exemple d’énoncé. C’est donc un bon test pour voir si LUIS peut savoir ce qui faut prédire avec cette intention.

    Le dernier paramètre de la chaîne de requête est `query`, l’énoncé est **requête**. Cet énoncé diffère de tous les exemples d’énoncés. Il constitue un bon test et doit retourner l’intention `OrderPizza` en tant qu’intention avec le score le plus élevé. 

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Le tableau d’entités est vide, car cette application n’a pas encore d’entités (unités de données d’un énoncé à extraire). 

    Le résultat JSON identifie l’intention avec le score le plus élevé en tant que propriété **`prediction.topIntent`** . Tous les scores sont compris entre 1 et 0, le meilleur score étant celui qui est le plus proche de 1. 

1. Modifiez paramètre **requête** de l’URL pour cibler l’intention **Salutations** :

    `Howdy`

    Ceci n’est pas exactement la même chose qu’un exemple d’énoncé. C’est donc un bon test pour voir si LUIS peut savoir ce qui faut prédire avec cette intention. 

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }    
    ```
 
    Cette prédiction a un score de confiance de 44 %. Pour augmenter le score de confiance, ajoutez entre 15 et 30 exemples d’énoncés.  

## <a name="client-application-next-steps"></a>Étapes suivantes de l’application cliente

Une fois que LUIS a retourné la réponse JSON, il en a fini avec cette demande. LUIS ne fournit pas de réponses aux énoncés de l’utilisateur ; il identifie uniquement le type d’informations demandé dans un langage naturel. Le suivi de la conversation est fourni par l’application cliente telle qu’un bot Azure. 


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Types d’entités](luis-concept-entity-types.md)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une application LUIS et des intentions, ajouté des exemples d’énoncés pour chaque intention, ajouté des exemples d’énoncés à l’intention None, puis effectué un apprentissage, publié et testé au niveau du point de terminaison. Ce sont les étapes de base de la génération d’un modèle LUIS. 

> [!div class="nextstepaction"]
> [Ajouter une entité décomposable à cette application](tutorial-machine-learned-entity.md)
