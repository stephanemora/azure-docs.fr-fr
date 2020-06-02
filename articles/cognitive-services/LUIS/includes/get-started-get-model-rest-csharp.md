---
title: Obtenir un modèle avec un appel REST en C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: b1bf3c0d7c902a048881b5fb75783744214b073e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655536"
---
## <a name="prerequisites"></a>Prérequis

* Azure Language Understanding - Clé de la ressource de création (32 caractères) et URL du point de terminaison de création. Créez-les dans le [portail Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou avec [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importez l’application [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) à partir du dépôt GitHub `Azure-Samples/cognitive-services-sample-data-files`.
* L’ID d’application LUIS pour l’application Pizza importée. L’ID d’application est indiqué dans le tableau de bord de l’application.
* L’ID de version dans l’application qui reçoit les énoncés.
* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Changer le modèle programmatiquement

1. Créez une application console ciblant le langage C#, avec un projet et le nom de dossier `csharp-model-with-rest`.

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. Accédez au répertoire `csharp-model-with-rest` que vous avez créé et installez les dépendances nécessaires avec les commandes suivantes :

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. Remplacez le contenu de Program.cs par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            static string appID = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            static string authoringKey = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this endpoint with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            static string endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number.
            static string appVersion = "0.1";

            static string host = String.Format("{0}luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }

            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }

            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                        'text': 'order a pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'order a large pepperoni pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 14,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 8,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'I want two large pepperoni pizzas on thin crust',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 17,
                                'endCharIndex': 32
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 11,
                                'endCharIndex': 15
                            },
                            {
                                'entityName': 'Quantity',
                                'startCharIndex': 7,
                                'endCharIndex': 9
                            },
                            {
                                'entityName': 'Crust',
                                'startCharIndex': 37,
                                'endCharIndex': 46
                            }
                        ]
                    }
                ]
                ";

                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Remplacez les valeurs commençant par `YOUR-` par vos propres valeurs.

    |Information|Objectif|
    |--|--|
    |`YOUR-APP-ID`| Votre ID d’application LUIS. |
    |`YOUR-AUTHORING-KEY`|Votre clé de création (32 caractères).|
    |`YOUR-AUTHORING-ENDPOINT`| L’URL de votre point de terminaison de création. Par exemple : `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Vous avez défini le nom de votre ressource au moment de sa création.|

    Les ressources et les clés affectées sont visibles dans le portail LUIS, dans la section Gérer de la page **Ressources Azure**. L’ID d’application est indiqué dans la section Gérer de la page **Paramètres de l’application**.

1. Générez l'application console.

    ```console
    dotnet build
    ```

1. Exécutez l’application console. La console affiche des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous aurez fini de suivre ce guide de démarrage rapide, supprimez le dossier de projet du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour une application](../luis-concept-best-practices.md)
