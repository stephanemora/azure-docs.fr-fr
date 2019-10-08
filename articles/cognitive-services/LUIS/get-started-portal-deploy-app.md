---
title: 'Démarrage rapide : Déployer une application avec le portail LUIS'
titleSuffix: Azure Cognitive Services
description: Découvrez comment déployer votre application LUIS sur le point de terminaison de prédiction une fois que l’application est prête à retourner des prédictions d’énoncés à une application cliente, par exemple un chatbot. Ce démarrage rapide vous guide tout au long du déploiement d’une application en créant une ressource de point de terminaison de prédiction, en affectant la ressource à l’application, en entraînant l’application et en la publiant.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f640921e6f48559db3f1414551d6ed974df15e4f
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703221"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Démarrage rapide : Déployer une application dans le portail LUIS

Lorsque votre application LUIS est prête à retourner des prédictions d’énoncés à une application cliente, par exemple un chatbot, vous devez déployer l’application sur le point de terminaison de prédiction.

Dans ce démarrage rapide, vous allez apprendre à déployer une application. Créez une ressource de point de terminaison de prédiction, affectez la ressource à l’application, formez l’application, puis publiez-la.

## <a name="prerequisites"></a>Prérequis

* Obtenez un [abonnement Azure](https://azure.microsoft.com/free).
* Suivez le guide de [démarrage rapide du portail précédent](get-started-portal-build-app.md) ou [téléchargez et importez l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Créer la ressource de point de terminaison

Vous créez la ressource de point de terminaison de prédiction dans le portail Azure. Cette ressource doit être utilisée seulement pour les requêtes de prédiction du point de terminaison. N’utilisez pas cette ressource pour apporter des modifications à l’application.

1. Connectez-vous au [Portail Azure](https://ms.portal.azure.com/).

1. Sélectionnez le signe **+** vert dans le volet supérieur gauche. Recherchez puis sélectionnez `Cognitive Services` dans la Place de marché.

1. Configurez l’abonnement avec les paramètres suivants :

   |Paramètre|Valeur|Objectif|
   |--|--|--|
   |Nom|`my-cognitive-service-resource`|Nom de la ressource Azure. Vous avez besoin de ce nom quand vous affectez la ressource à l’application dans le portail LUIS.|
   |Subscription|Votre abonnement|Sélectionnez un des abonnements associés à votre compte.|
   |Location|**USA Ouest**|Région Azure pour cette ressource.|
   |Niveau tarifaire|**S0**|Niveau tarifaire par défaut pour cette ressource.|
   |Resource group|`my-cognitive-service-resource-group`|Créez un groupe de ressources pour toutes vos ressources Cognitive Services. Quand vous en avez terminé avec les ressources, vous pouvez supprimer le groupe de ressources pour nettoyer votre abonnement. |
   | | | |

   ![Choix d’API Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Sélectionnez **Créer** pour créer la ressource Azure.

   Dans la section suivante, vous découvrez comment connecter cette nouvelle ressource à une application LUIS dans le portail LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Affecter la clé de ressource à l’application de LUIS dans le portail LUIS

Chaque fois que vous créez une ressource pour LUIS, vous devez l’affecter à l’application LUIS. Une fois qu’elle est affectée, vous n’avez plus besoin d’effectuer cette étape, sauf si vous créez une nouvelle ressource. Vous pouvez être amené à créer une nouvelle ressource pour étendre les régions de votre application ou pour prendre en charge un nombre plus élevé de requêtes de prédiction.

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis choisissez l’application **myEnglishApp** dans la liste des applications.

1. Dans le menu supérieur droit, sélectionnez **Manage** (Gérer), puis **Azure Resources** (Ressources Azure).

1. Pour ajouter l’application LUIS, sélectionnez **Add prediction resource** (Ajouter une ressource de prédiction).

    <!-- TBD: get screenshot-->

1. Sélectionnez vos locataire, abonnement et nom de ressource. Sélectionnez **Assign resource** (Affecter une ressource).

   ![Affecter une ressource à votre application](./media/get-started-portal-deploy-app/assign-resource.png)

1. Recherchez la nouvelle ligne dans le tableau et copiez l’URL de point de terminaison. Elle est correctement construite pour effectuer une requête `HTTP GET` au point de terminaison d’API LUIS pour une prédiction.

## <a name="train-and-publish-the-app"></a>Former et publier l’application

Formez l’application lorsque vous êtes prêt à la tester. Publiez l’application lorsque vous voulez que la version actuellement entraînée soit disponible pour les applications clientes auprès du runtime du point de terminaison de prédiction de la requête.

1. Si l’application n’est pas entraînée, sélectionnez **Entraîner** dans le menu en haut à droite.

1. Sélectionnez **Publuer** dans le menu supérieur. Acceptez les paramètres d’environnement par défaut, puis sélectionnez **Publier**.

1. Quand la barre de notification de réussite de couleur verte s’affiche en haut de la fenêtre du navigateur, sélectionnez **Consulter la liste des points de terminaison**.

   ![Barre de notification dans le navigateur, indiquant que l’application a été publiée](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. La liste des ressources affectées et des URL de point de terminaison correspondantes se trouve au bas de la page **Keys and Endpoint settings** (Paramètres des clés et du point de terminaison).

1. Sélectionnez l’URL de point de terminaison associée au nom de votre nouvelle ressource. Cette action ouvre un navigateur web avec une URL correctement construite pour effectuer une requête `GET` auprès du runtime du point de terminaison de prédiction.

## <a name="prediction-endpoint-request"></a>Demande de point de terminaison de prédiction

<!-- V3FIX -->

Le `q=` à la fin de l’URL est l’abréviation de **query** et est l’endroit où l’énoncé de l’utilisateur est ajouté à la requête GET. Après le `q=`, entrez le même énoncé utilisateur que celui utilisé à la fin du guide de démarrage rapide précédent :

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

Cette réponse vous donne plus d’informations que le volet de test par défaut dans le tutoriel précédent. Pour voir ce même niveau d’informations dans le volet de test, vous devez publier l’application. Une fois l’application publiée, sélectionnez **Comparer avec la version publiée** dans le volet de test. Utilisez **Afficher la vue JSON** dans le volet de test publié pour voir le même code JSON qu’à l’étape précédente. Vous pouvez ainsi comparer l’application actuelle sur laquelle vous travaillez et une application publiée sur le point de terminaison.

[![Comparer la version en cours de modification avec la version publiée de l’application](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)




## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez terminé ce guide de démarrage rapide, sélectionnez **Mes applications** dans le menu de navigation supérieur. Cochez la case de l’application dans la liste, puis sélectionnez **Supprimer** dans la barre d’outils contextuelle au-dessus de la liste.

[![Supprimer l’application de la liste Mes applications](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Identifier les intentions et les entités courantes](luis-tutorial-prebuilt-intents-entities.md)
