---
title: Obtenir des prédictions avec l’appel REST en C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733338"
---
## <a name="prerequisites"></a>Prérequis

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Un ID d’application LUIS : utilisez l’ID d’application IoT publique `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La requête utilisateur utilisée dans le code du guide de démarrage rapide est propre à cette application.

## <a name="create-luis-runtime-key-for-predictions"></a>Créer une clé Runtime LUIS pour les prédictions

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur [Créer**Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Entrez tous les paramètres obligatoires pour la clé de runtime :

    |Paramètre|Valeur|
    |--|--|
    |Nom|Nom de votre choix (2-64 caractères)|
    |Abonnement|Sélectionner l’abonnement approprié|
    |Emplacement|Sélectionnez n’importe quel emplacement disponible et proche|
    |Niveau de tarification|`F0` - le niveau tarifaire minimal|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources.
1. Collectez la valeur `endpoint` configurée et une valeur `key`.

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Utilisez C# (.NET Core) pour interroger le [point de terminaison de prédiction](https://aka.ms/luis-apim-v3-prediction) afin d’obtenir le résultat de la prédiction.

1. Créez une application console ciblant le langage C#, avec un projet et le nom de dossier `predict-with-rest`.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Accédez au répertoire `predict-with-rest` que vous venez de créer et installez les dépendances nécessaires avec les commandes suivantes :

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Ouvrez `Program.cs` dans votre IDE ou votre éditeur de texte favori. Ensuite, remplacez `Program.cs` par le code suivant :

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Remplacez les valeurs `YOUR-KEY` et `YOUR-ENDPOINT` par votre clé et votre point de terminaison de prédiction.

    |Information|Objectif|
    |--|--|
    |`YOUR-KEY`|Votre clé de prédiction (32 caractères).|
    |`YOUR-ENDPOINT`| L’URL de votre point de terminaison de prédiction. Par exemple : `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Générez l’application console avec cette commande :

    ```console
    dotnet build
    ```

1. Exécutez l’application console. La console affiche des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur.

    ```console
    dotnet run
    ```

1. Examinez la réponse de prédiction, qui est retournée au format JSON :

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    La réponse JSON mise en forme pour des raisons de lisibilité :

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
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

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous aurez fini de suivre ce guide de démarrage rapide, supprimez le fichier du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés et effectuer l’apprentissage de](../get-started-get-model-rest-apis.md)