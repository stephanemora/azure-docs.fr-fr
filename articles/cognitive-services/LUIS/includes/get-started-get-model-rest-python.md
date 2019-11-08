---
title: Obtenir un modèle avec un appel REST en C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 8cefd8357893657d94959cb853004b34b0ec9d8d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505842"
---
## <a name="prerequisites"></a>Prérequis

* Clé de démarrage.
* Importez l’application [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) à partir du référentiel GitHub cognitive-services-language-understanding.
* L’ID d’application LUIS pour l’application TravelAgent importée. L’ID d’application est indiqué dans le tableau de bord de l’application.
* L’ID de version dans l’application qui reçoit les énoncés. L’ID par défaut est « 0.1 ».
* [Python 3.6](https://www.python.org/downloads/) ou version ultérieure.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]


## <a name="change-model-programmatically"></a>Modifier le modèle par programmation

Utilisez Go pour ajouter à l’application une [API](https://aka.ms/luis-apim-v3-authoring) d’entité issue de l’apprentissage automatique. 

1. Créez un nouveau fichier appelé `model.py`. Ajoutez le code suivant :

    ```python
    ########### Python 3.6 #############
    import requests
    
    # Starter key
    LUIS_authoringKey  = "YOUR-KEY"
    
    LUIS_APP_ID = "YOUR-APP-ID"
    
    # Authoring endpoint, example: westus.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"
    
    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/train'
    
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
1. Remplacez les valeurs suivantes :

    * `YOUR-KEY` par votre clé de démarrage
    * `YOUR-ENDPOINT` par votre point de terminaison, par exemple, `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` par l’ID de votre application

1. Avec une invite de commandes située dans le même répertoire que celui où vous avez créé le fichier, entrez la commande suivante pour exécuter le fichier :

    ```console
    python model.py
    ```  

## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous aurez fini de suivre ce guide de démarrage rapide, supprimez le fichier du système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour une application](../luis-concept-best-practices.md)