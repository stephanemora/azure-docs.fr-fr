---
title: Obtenir un modèle avec un appel REST en C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a21834cec456ded82c15dc916cc4991a196ea22c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966701"
---
## <a name="prerequisites"></a>Conditions préalables requises

* Azure Language Understanding - Clé de la ressource de création (32 caractères) et URL du point de terminaison de création. Créez-les dans le [portail Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou avec [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importez l’application [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) à partir du dépôt GitHub cognitive-services-language-understanding.
* L’ID d’application LUIS pour l’application TravelAgent importée. L’ID d’application est indiqué dans le tableau de bord de l’application.
* L’ID de version dans l’application qui reçoit les énoncés. L’ID par défaut est « 0.1 ».
* [Python 3.6](https://www.python.org/downloads/) ou version ultérieure.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Changer le modèle programmatiquement

Utilisez Go pour ajouter à l’application une [API](https://aka.ms/luis-apim-v3-authoring) d’entité issue du Machine Learning.

1. Créez un nouveau fichier appelé `model.py`. Ajoutez le code suivant :

    ```python
    ########### Python 3.6 #############
    import requests

    # 32 character Authoring key
    LUIS_authoringKey  = "YOUR-KEY"

    LUIS_APP_ID = "YOUR-APP-ID"

    # Authoring endpoint, example: your-resource-name.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"

    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/train'

    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}

    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())

    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())

    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())

    addUtterances()
    train()
    trainStatus()
    ```

1. Remplacez les valeurs commençant par `YOUR-` avec vos propres valeurs.

    |Information|Objectif|
    |--|--|
    |`YOUR-KEY`|Votre clé de création (32 caractères).|
    |`YOUR-ENDPOINT`| L’URL de votre point de terminaison de création. Par exemple : `replace-with-your-resource-name.api.cognitive.microsoft.com`. Vous avez défini le nom de votre ressource au moment de sa création.|
    |`YOUR-APP-ID`| Votre ID d’application LUIS. |

    Les ressources et les clés affectées sont visibles dans le portail LUIS, dans la section Gérer de la page **Ressources Azure**. L’ID d’application est indiqué dans la section Gérer de la page **Paramètres de l’application**.

1. Avec une invite de commandes située dans le même répertoire que celui où vous avez créé le fichier, entrez la commande suivante pour exécuter le fichier :

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous aurez fini de suivre ce guide de démarrage rapide, supprimez le fichier du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour une application](../luis-concept-best-practices.md)
