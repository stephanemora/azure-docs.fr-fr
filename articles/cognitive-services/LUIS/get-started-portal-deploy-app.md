---
title: 'Démarrage rapide : Déployer une application avec le portail LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Une fois que l’application est prête à retourner des prédictions d’énoncés à une application cliente, par exemple un chatbot, vous devez déployer l’application sur le point de terminaison de prédiction. Dans ce guide de démarrage rapide, vous découvrez comment déployer une application en créant une ressource de point de terminaison de prédiction, en affectant la ressource à l’application, en entraînant l’application et en la publiant.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259718"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Démarrage rapide : Déployer une application dans le portail LUIS

Une fois que l’application est prête à retourner des prédictions d’énoncés à une application cliente, par exemple un chatbot, vous devez déployer l’application sur le point de terminaison de prédiction. 

Dans ce guide de démarrage rapide, vous découvrez comment déployer une application en créant une ressource de point de terminaison de prédiction, en affectant la ressource à l’application, en entraînant l’application et en la publiant. 

## <a name="prerequisites"></a>Prérequis

* [Abonnement Azure](https://azure.microsoft.com/free).
* Suivez le guide de [démarrage rapide du portail précédent](get-started-portal-build-app.md) ou [téléchargez et importez l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Créer une ressource de point de terminaison

La ressource de point de terminaison de prédiction est créée dans le portail Azure. Cette ressource doit être utilisée seulement pour les requêtes de prédiction du point de terminaison. N’utilisez pas cette ressource pour apporter des modifications à l’application. 

1. Connectez-vous au **[portail Azure](https://ms.portal.azure.com/)**. 

1. Sélectionnez le signe **+** vert connecter dans le volet supérieur gauche et recherchez `Cognitive Services` dans la Place de marché, puis sélectionnez-le. 

1. Configurez l’abonnement avec les paramètres suivants :

    |Paramètre|Valeur|Objectif|
    |--|--|--|
    |Nom|`my-cognitive-service-resource`|Nom de la ressource Azure. Vous avez besoin de ce nom quand vous affectez la ressource à l’application dans le portail LUIS.|
    |Abonnement|Votre abonnement|Sélectionnez un des abonnements associés à votre compte.|
    |Lieu|**USA Ouest**|Région Azure pour cette ressource.|
    |Niveau tarifaire|**S0**|Niveau tarifaire par défaut pour cette ressource.|
    |Groupe de ressources|`my-cognitive-service-resource-group`|Créez un groupe de ressources pour toutes vos ressources Cognitive Services. Quand vous en avez terminé avec les ressources, vous pouvez supprimer le groupe de ressources pour nettoyer votre abonnement. | 

    ![Choix d’API Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Sélectionnez **Créer** pour créer la ressource Azure. 

    Dans la section suivante, vous découvrez comment connecter cette nouvelle ressource à une application LUIS dans le portail LUIS. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Affecter la clé de ressource à l’application de LUIS dans le portail LUIS

Chaque fois que vous créez une ressource pour LUIS, vous devez l’affecter à l’application LUIS. Une fois qu’elle est affectée, vous n’avez plus besoin d’effectuer cette étape, sauf si vous créez une nouvelle ressource. Vous pouvez être amené à créer une nouvelle ressource pour étendre les régions de votre application ou pour prendre en charge un nombre plus élevé de requêtes de prédiction. 

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis choisissez l’application **myEnglishApp** dans la liste des applications.

1. Sélectionnez **Gérer** dans le menu en haut à droite, puis sélectionnez **Clés et points de terminaison**.

1. Pour ajouter la clé LUIS, sélectionnez **Assign Resource +** (Affecter une ressource).

    [![Affecter une ressource à votre application](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Sélectionnez votre locataire, l’abonnement et le nom de la ressource, puis sélectionnez **Affecter la ressource**. 

    ![Affecter une ressource à votre application](./media/get-started-portal-deploy-app/assign-resource.png)

1. Recherchez la nouvelle ligne dans le tableau et copiez l’URL de point de terminaison. Elle est correctement construite pour effectuer une requête HTTP GET au point de terminaison d’API LUIS pour une prédiction. 

## <a name="train-and-publish-the-app"></a>Former et publier l’application 

Vous devez effectuer l’entraînement chaque fois que vous êtes prêt à la tester. Vous devez publier l’application chaque fois que vous voulez que la version actuellement entraînée soit disponible pour les applications clientes auprès du runtime du point de terminaison de prédiction. 

1. Si l’application n’est pas entraînée, sélectionnez **Entraîner** dans le menu en haut à droite.

1. Dans le menu en haut à droite, sélectionnez **Publier**. Acceptez les paramètres d’environnement par défaut, puis sélectionnez **Publier**.

1. Quand la barre de notification de réussite de couleur verte s’affiche en haut de la fenêtre du navigateur, sélectionnez le lien **Consulter la liste des points de terminaison**. 

    ![Barre de notification dans le navigateur, indiquant que l’application a été publiée](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Ceci vous dirige vers la page **Paramètres des clés et du point de terminaison**. La liste des ressources affectées et des URL de point de terminaison correspondantes se trouve dans le bas de la page. 

1. Sélectionnez l’URL de point de terminaison associée au nom de votre nouvelle ressource. Ceci ouvre un navigateur web avec une URL correctement construite pour effectuer une requête **GET** auprès du runtime du point de terminaison de prédiction. 

1. Le `q=` à la fin de l’URL est l’abréviation de **query** et est l’endroit où l’énoncé de l’utilisateur est ajouté à la requête GET. Après le `q=`, entrez le même énoncé utilisateur que celui utilisé à la fin du guide de démarrage rapide précédent :

    ```Is there a form named hrf-234098```

    La réponse du navigateur est le même code JSON que celui reçu par votre application cliente :

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Cette réponse vous donne plus d’informations que le volet **Test** par défaut dans le tutoriel précédent. Si vous voulez voir ce même niveau d’informations dans le volet Test, l’application doit être publiée. Ensuite, dans le volet Test, sélectionnez **Comparer avec la version publiée**. Utilisez **Afficher la vue JSON** dans le volet de test publié pour voir le même code JSON qu’à l’étape précédente. Ceci vous permet de comparer l’application actuelle sur laquelle vous travaillez et l’application qui est publiée sur le point de terminaison.

    [![Comparer la version en cours de modification avec la version publiée de l’application](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous avez terminé ce guide de démarrage rapide, sélectionnez **Mes applications** dans le menu de navigation supérieur. Cochez ensuite la case à gauche de l’application dans la liste, puis sélectionnez **Supprimer** dans la barre d’outils contextuelle au-dessus de la liste. 

[![Supprimer l’application de la liste Mes applications](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Identifier les intentions et les entités courantes](luis-tutorial-prebuilt-intents-entities.md)