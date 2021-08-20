---
title: Démarrage rapide du service Azure SignalR serverless - C#
description: Démarrage rapide montrant comment utiliser Azure SignalR Service et Azure Functions pour créer une application indiquant le nombre d’étoiles GitHub en utilisant C#.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 06/09/2021
ms.author: zhshang
ms.openlocfilehash: 1856f6e012c2b90e173162f055d64402f0c4c908
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112462106"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-c"></a>Démarrage rapide : Créer une application qui indique le nombre d’étoiles GitHub avec Azure Functions and SignalR Service en utilisant C\#

Le service Azure SignalR vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce démarrage rapide, découvrez comment utiliser SignalR Service et Azure Functions pour créer une application serverless avec C# afin de diffuser des messages aux clients.

> [!NOTE]
> Tous les codes mentionnés dans l’article sont accessibles sur le site [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/csharp).

## <a name="prerequisites"></a>Prérequis

Si Visual Studio Code n’est pas encore installé, vous pouvez le télécharger et l’utiliser gratuitement (https://code.visualstudio.com/Download) ).

Vous pouvez également exécuter ce tutoriel sur une ligne de commande (macOS, Windows ou Linux) à l’aide d’[Azure Functions Core Tools](../azure-functions/functions-run-local.md?tabs=windows%2Ccsharp%2Cbash#v2). De même, vous pouvez utiliser le [kit SDK .NET Core](https://dotnet.microsoft.com/download) et votre éditeur de code favori.

Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/dotnet) avant de commencer.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure-and-create-signalr-service-instance"></a>Se connecter à Azure et créer une instance SignalR Service

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qscsharp).

## <a name="setup-and-run-the-azure-function-locally"></a>Configurer et exécuter la fonction Azure localement

1. Vérifiez qu’Azure Functions Core Tools est installé. Ensuite, créez un répertoire vide et accédez-y depuis la ligne de commande.

    ```bash
    # Initialize a function project
    func init --worker-runtime dotnet
    
    # Add SignalR Service package reference to the project
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService
    ```

2. Après avoir initialisé un projet, créez un nouveau fichier portant le nom *Function.cs*. Ajoutez le code suivant à *Function.cs*.
   
    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Newtonsoft.Json;
    
    namespace CSharp
    {
        public static class Function
        {
            private static HttpClient httpClient = new HttpClient();
    
            [FunctionName("index")]
            public static IActionResult Index([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, ExecutionContext context)
            {
                var path = Path.Combine(context.FunctionAppDirectory, "content", "index.html");
                return new ContentResult
                {
                    Content = File.ReadAllText(path),
                    ContentType = "text/html",
                };
            }
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo Negotiate( 
                [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req,
                [SignalRConnectionInfo(HubName = "serverlessSample")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static async Task Broadcast([TimerTrigger("*/5 * * * * *")] TimerInfo myTimer,
            [SignalR(HubName = "serverlessSample")] IAsyncCollector<SignalRMessage> signalRMessages)
            {
                var request = new HttpRequestMessage(HttpMethod.Get, "https://api.github.com/repos/azure/azure-signalr");
                request.Headers.UserAgent.ParseAdd("Serverless");
                var response = await httpClient.SendAsync(request);
                var result = JsonConvert.DeserializeObject<GitResult>(await response.Content.ReadAsStringAsync());
                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { $"Current start count of https://github.com/Azure/azure-signalr is: {result.StartCount}" }
                    });
            }
    
            private class GitResult
            {
                [JsonRequired]
                [JsonProperty("stargazers_count")]
                public string StartCount { get; set; }
            }
        }
    }
    ```
    Ces codes ont trois fonctions. `Index` est utilisé pour obtenir un site web en tant que client. `Negotiate` est utilisé pour que le client obtienne un jeton d’accès. `Broadcast` récupère régulièrement le nombre d’étoiles auprès de GitHub et diffuse des messages à tous les clients.

3. L’interface client de cet exemple est une page web. En considérant que nous lisons le contenu HTML de `content/index.html` dans la fonction `GetHomePage`, créez un nouveau fichier `index.html` dans le répertoire `content`. Ensuite, copiez le contenu suivant.
    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```

4. C’est presque terminé. La dernière étape consiste à définir une chaîne de connexion de SignalR Service dans les paramètres d’Azure Functions.

    1. Dans le navigateur dans lequel le portail Azure est ouvert, vérifiez que l’instance du service SignalR que vous avez déployée précédemment a bien été créée en recherchant son nom dans la zone de recherche en haut du portail. Sélectionnez l’instance pour l’ouvrir.

        ![Rechercher l’instance du service SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. Sélectionnez **Clés** pour afficher les chaînes de connexion de l’instance du service SignalR.
    
        ![Capture d’écran qui met en évidence la chaîne de connexion principale.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. Copiez la chaîne de connexion principale. Puis exécutez la commande ci-dessous.
    
        ```bash
        func settings add AzureSignalRConnectionString '<signalr-connection-string>'
        ```
    
5. Exécutez la fonction Azure en local :

    ```bash
    func start
    ```

    Après l’exécution locale de la fonction Azure, utilisez votre navigateur pour accéder à l’adresse `http://localhost:7071/api/index` et voir le nombre actuel d’étoiles. Si vous ajoutez ou supprimez une étoile sur le site GitHub, le nombre d’étoiles sera actualisé toutes les quelques secondes.

    > [!NOTE]
    > La liaison SignalR nécessite Stockage Azure, mais vous pouvez utiliser l’émulateur de stockage local lorsque la fonction est exécutée localement.
    > Si vous rencontrez des erreurs telles que `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, vous devez télécharger et activer l’[émulateur de stockage](../storage/common/storage-use-emulator.md).

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez généré et exécuté une application serverless en temps réel en local. Découvrez comment utiliser les liaisons SignalR Service pour Azure Functions.
Ensuite, découvrez comment permettre une communication bidirectionnelle entre les clients et Azure Function grâce à SignalR Service.

> [!div class="nextstepaction"]
> [Liaisons de service SignalR pour Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Communication bidirectionnelle dans Serverless](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Développer des fonctions Azure à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md)

