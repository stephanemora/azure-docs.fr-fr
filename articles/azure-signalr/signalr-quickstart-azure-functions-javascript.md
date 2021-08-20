---
title: Utiliser JavaScript pour créer une salle de conversation avec Azure Functions et SignalR Service
description: Démarrage rapide montrant comment utiliser Azure SignalR Service et Azure Functions pour créer une application indiquant le nombre d’étoiles GitHub en utilisant JavaScript.
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: javascript
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 9e8bb3d49ef236521f7d4a48060b2405dbbbc104
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112462016"
---
# <a name="quickstart-use-javascript-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>Démarrage rapide : Utiliser JavaScript pour créer une application qui indique le nombre d’étoiles GitHub avec Azure Functions et SignalR Service

Azure SignalR Service vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application, et Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructure. Dans ce démarrage rapide, découvrez comment utiliser SignalR Service et Azure Functions pour créer une application serverless avec JavaScript afin de diffuser des messages aux clients.

> [!NOTE]
> Tous les codes mentionnés dans l’article sont accessibles sur le site [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/javascript).

## <a name="prerequisites"></a>Prérequis

- Un éditeur de code, comme [Visual Studio Code](https://code.visualstudio.com/).
- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), version 2 ou supérieure. Sert à exécuter les applications Azure Functions localement.
- [Node.js](https://nodejs.org/en/download/), version 10.x.

   > [!NOTE]
   > Les exemples devraient fonctionner avec d’autres versions Node.js. Pour plus d’informations, consultez la [documentation des versions du runtime Azure Functions](../azure-functions/functions-versions.md#languages).

> [!NOTE]
> Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjs).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjs).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjs).


## <a name="setup-and-run-the-azure-function-locally"></a>Configurer et exécuter la fonction Azure localement

1. Vérifiez qu’Azure Functions Core Tools est installé. Ensuite, créez un répertoire vide et accédez-y depuis la ligne de commande.

    ```bash
    # Initialize a function project
    func init --worker-runtime javascript
    ```

2. Après avoir initialisé un projet, vous devez créer des fonctions. Dans cet exemple, nous devons créer trois fonctions.

    1. Exécutez la commande suivante pour créer une fonction `index`, destinée à héberger une page web pour le client.

        ```bash
        func new -n index -t HttpTrigger
        ```
        
        Ouvrez `index/index.js` et copiez les codes suivants.

        ```javascript
        var fs = require('fs').promises
    
        module.exports = async function (context, req) {
            const path = context.executionContext.functionDirectory + '/../content/index.html'
            try {
                var data = await fs.readFile(path);
                context.res = {
                    headers: {
                        'Content-Type': 'text/html'
                    },
                    body: data
                }
                context.done()
            } catch (error) {
                context.log.error(err);
                context.done(err);
            }
        }
        ```
    
    2. Créez une fonction `negotiate` pour que les clients obtiennent un jeton d’accès.
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        Ouvrez `negotiate/function.json` et copiez les codes json suivants :
    
        ```json
        {
          "disabled": false,
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "methods": [
                "post"
              ],
              "name": "req",
              "route": "negotiate"
            },
            {
              "type": "http",
              "direction": "out",
              "name": "res"
            },
            {
              "type": "signalRConnectionInfo",
              "name": "connectionInfo",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "in"
            }
          ]
        }
        ```
    
    3. Créez une fonction `broadcast` pour diffuser des messages à tous les clients. Dans l’exemple, nous utilisons un déclencheur temporel pour diffuser des messages régulièrement.
    
        ```bash
        func new -n broadcast -t TimerTrigger
        ```
    
        Ouvrez `broadcast/function.json` et copiez les codes suivants.
    
        ```json
        {
          "bindings": [
            {
              "name": "myTimer",
              "type": "timerTrigger",
              "direction": "in",
              "schedule": "*/5 * * * * *"
            },
            {
              "type": "signalR",
              "name": "signalRMessages",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "out"
            }
          ]
        }
        ```
    
        Ouvrez `broadcast/index.js` et copiez les codes suivants.
    
        ```javascript
        var https = require('https');
        
        module.exports = function (context) {
            var req = https.request("https://api.github.com/repos/azure/azure-signalr", {
                method: 'GET',
                headers: {'User-Agent': 'serverless'}
            }, res => {
                var body = "";
        
                res.on('data', data => {
                    body += data;
                });
                res.on("end", () => {
                    var jbody = JSON.parse(body);
                    context.bindings.signalRMessages = [{
                        "target": "newMessage",
                        "arguments": [ `Current star count of https://github.com/Azure/azure-signalr is: ${jbody['stargazers_count']}` ]
                    }]
                    context.done();
                });
            }).on("error", (error) => {
                context.log(error);
                context.res = {
                  status: 500,
                  body: error
                };
                context.done();
            });
            req.end();
        }    
        ```

3. L’interface client de cet exemple est une page web. En considérant que nous lisons le contenu HTML de `content/index.html` dans la fonction `index`, créez un nouveau fichier `index.html` dans le répertoire `content`. Ensuite, copiez le contenu suivant.

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

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjs).

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez généré et exécuté une application serverless en temps réel en local. Découvrez comment utiliser les liaisons SignalR Service pour Azure Functions.
Ensuite, découvrez comment permettre une communication bidirectionnelle entre les clients et Azure Function grâce à SignalR Service.

> [!div class="nextstepaction"]
> [Liaisons de service SignalR pour Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Communication bidirectionnelle dans Serverless](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Déployer Azure Functions avec VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
