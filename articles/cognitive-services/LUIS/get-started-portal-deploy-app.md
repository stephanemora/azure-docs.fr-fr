---
title: 'Démarrage rapide : Déployer une application avec le portail LUIS'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment déployer une application en créant une ressource de point de terminaison de prédiction, en affectant la ressource à l’application, puis en entraînant et en publiant l’application.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 65af2caa2272549b5ad562ff3c38b90e3ea43fd5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278540"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Démarrage rapide : Déployer une application dans le portail LUIS

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Lorsque votre application LUIS est prête à retourner des prédictions d’énoncés à une application cliente, par exemple un chatbot, vous devez déployer l’application sur le point de terminaison de prédiction.

Dans ce démarrage rapide, vous allez apprendre à déployer une application. Créez une ressource de point de terminaison de prédiction, affectez la ressource à l’application, formez l’application, puis publiez-la.

## <a name="prerequisites"></a>Prérequis

* Obtenez un [abonnement Azure](https://azure.microsoft.com/free).
* Suivez le guide de [démarrage rapide du portail précédent](get-started-portal-build-app.md) ou [téléchargez et importez l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Si vous disposez d’applications qui sont antérieures à l’authentification des ressources Azure, [effectuez une migration vers une ressource Azure](luis-migration-authoring.md). Certaines pages du portail s’affichent différemment lorsque l’authentification par e-mail est appliquée.

## <a name="create-the-endpoint-resource"></a>Créer la ressource de point de terminaison

Vous créez la ressource de point de terminaison de prédiction dans le portail Azure. Cette ressource doit être utilisée seulement pour les requêtes de prédiction du point de terminaison. N’utilisez pas cette ressource pour apporter des modifications à l’application.

1. Connectez-vous et créez une ressource dans le [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Configurez l’abonnement avec les paramètres suivants :

   |Paramètre|Valeur|Objectif|
   |--|--|--|
   |Nom|`my-luis-resource`|Nom de la ressource Azure. Vous avez besoin de ce nom quand vous affectez la ressource à l’application dans le portail LUIS.|
   |Subscription|Votre abonnement|Sélectionnez un des abonnements associés à votre compte.|
   |Resource group|`my-resource-group`|Créez un groupe de ressources pour toutes vos ressources Cognitive Services. Quand vous en avez terminé avec les ressources, vous pouvez supprimer le groupe de ressources pour nettoyer votre abonnement. |
   |Emplacement de création|**USA Ouest**|Région Azure pour la création.|
   |Niveau tarifaire de création|**F0**|Niveau tarifaire par défaut pour la création.|
   |Emplacement du runtime|**USA Ouest**|Région Azure pour les requêtes de point de terminaison de prédiction.|
   |Niveau tarifaire du runtime|**S0**|Ce niveau tarifaire est fourni pour les sites web à fort trafic.|
   | | | |


   ![Choix d’API Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Sélectionnez **Créer** pour créer la ressource Azure.

   Dans la section suivante, vous découvrez comment connecter cette nouvelle ressource à une application LUIS dans le portail LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Affecter la clé de ressource à l’application de LUIS dans le portail LUIS

Chaque fois que vous créez une ressource pour LUIS, vous devez l’affecter à l’application LUIS. Une fois qu’elle est affectée, vous n’avez plus besoin d’effectuer cette étape, sauf si vous créez une nouvelle ressource. Vous pouvez être amené à créer une nouvelle ressource pour étendre les régions de votre application ou pour prendre en charge un nombre plus élevé de requêtes de prédiction.

1. Connectez-vous au [portail LUIS](https://preview.luis.ai), puis choisissez l’application **myEnglishApp** dans la liste des applications.

1. Dans le menu supérieur droit, sélectionnez **Manage** (Gérer), puis **Azure Resources** (Ressources Azure).

1. Pour ajouter l’application LUIS, sélectionnez **Add prediction resource** (Ajouter une ressource de prédiction).

    ![Pour ajouter la ressource de prédiction LUIS, sélectionnez Add prediction resource (Ajouter une ressource de prédiction).](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Sélectionnez vos locataire, abonnement et nom de ressource. Sélectionnez **Assign resource** (Affecter une ressource).

   ![Affecter une ressource à votre application](./media/get-started-portal-deploy-app/assign-resource.png)

1. Effectuez les mêmes étapes pour ajouter la clé de création à votre application.

1. Recherchez la nouvelle ligne dans le tableau pour la nouvelle ressource de prédiction et copiez l’URL de point de terminaison. Elle est correctement construite pour effectuer une requête `HTTP GET` au point de terminaison d’API LUIS pour une prédiction.

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publier l’application LUIS sur le point de terminaison de prédiction

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Demande de point de terminaison de prédiction

Dans le portail en préversion, `query=` à la fin de l’URL représente l’endroit où l’énoncé de l’utilisateur est ajouté à la requête GET. Après le `query=`, entrez le même énoncé utilisateur que celui utilisé à la fin du guide de démarrage rapide précédent :

```Is there a form named hrf-234098```

Assurez-vous que la chaîne de requête comprend les paires suivantes :

* `show-all-intents=true`
* `verbose=true`

Le navigateur affiche la réponse :

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Pour voir ce même niveau d’informations dans le volet de test, vous devez publier l’application. Une fois l’application publiée, sélectionnez **Comparer avec la version publiée** dans le volet de test. Utilisez **Afficher la vue JSON** dans le volet de test publié pour voir le même code JSON qu’à l’étape précédente. Vous pouvez ainsi comparer les modifications apportées à l’application actuelle sur laquelle vous travaillez avec une application publiée sur le point de terminaison.

[![Comparer la version en cours de modification avec la version publiée de l’application](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez terminé ce guide de démarrage rapide, sélectionnez **Mes applications** dans le menu de navigation supérieur. Cochez la case de l’application dans la liste, puis sélectionnez **Supprimer** dans la barre d’outils contextuelle au-dessus de la liste.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Identifier les intentions et les entités courantes](luis-tutorial-prebuilt-intents-entities.md)
