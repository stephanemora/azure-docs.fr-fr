---
title: Démarrage rapide du service Azure SignalR serverless - Python
description: Guide de démarrage rapide montrant comment utiliser Azure SignalR Service et Azure Functions pour créer une application indiquant le nombre d’étoiles GitHub en utilisant Python.
author: anthonychu
ms.author: antchu
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 46c15f932f55883be66745d415820767089ae0f1
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461998"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-python"></a>Démarrage rapide : Créer une application qui indique le nombre d’étoiles GitHub avec Azure Functions and SignalR Service en utilisant Python

Le service Azure SignalR vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce guide de démarrage rapide, découvrez comment utiliser SignalR Service et Azure Functions pour créer une application serverless avec Python afin de diffuser des messages aux clients.

> [!NOTE]
> Tous les codes mentionnés dans l’article sont accessibles sur le site [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/python).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.

Assurez-vous qu’un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/) est installé.

Installez [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (version 2.7.1505 ou ultérieure) pour exécuter des applications Azure Functions Python en local.

Azure Functions nécessite [Python 3.6+](https://www.python.org/downloads/). (Voir [Versions de Python prises en charge](../azure-functions/functions-reference-python.md#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qspython).


## <a name="setup-and-run-the-azure-function-locally"></a>Configurer et exécuter la fonction Azure localement

1. Vérifiez qu’Azure Functions Core Tools est installé. Ensuite, créez un répertoire vide et accédez-y depuis la ligne de commande.

    ```bash
    # Initialize a function project
    func init --worker-runtime python
    ```

2. Après avoir initialisé un projet, vous devez créer des fonctions. Dans cet exemple, nous devons créer 3 fonctions.

    1. Exécutez la commande suivante pour créer une fonction `index`, destinée à héberger une page web pour le client.

        ```bash
        func new -n index -t HttpTrigger
        ```
        
        Ouvrez `index/__init__.py` et copiez les codes suivants.

        ```javascript
        import os
    
        import azure.functions as func
        
        
        def main(req: func.HttpRequest) -> func.HttpResponse:
            f = open(os.path.dirname(os.path.realpath(__file__)) + '/../content/index.html')
            return func.HttpResponse(f.read(), mimetype='text/html')
        ```
    
    2. Créez une fonction `negotiate`pour que les clients obtiennent un jeton d’accès.
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        Ouvrez `negotiate/function.json` et copiez les codes json suivants :
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "authLevel": "function",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "$return"
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

        Ensuite, ouvrez `negotiate/__init__.py` et copiez les codes json suivants :

        ```python
        import azure.functions as func
    
        
        def main(req: func.HttpRequest, connectionInfo) -> func.HttpResponse:
            return func.HttpResponse(connectionInfo)
        ```
    
    3. Créez une fonction `broadcast` pour diffuser des messages à tous les clients. Dans l’exemple, nous utilisons un déclencheur de temps pour diffuser des messages régulièrement.
    
        ```bash
        func new -n broadcast -t TimerTrigger
        # install requests
        pip install requests
        ```
    
        Ouvrez `broadcast/function.json` et copiez les codes suivants.
    
        ```json
        {
          "scriptFile": "__init__.py",
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
    
        Ouvrez `broadcast/__init__.py` et copiez les codes suivants.
    
        ```python
        import requests
        import json
        
        import azure.functions as func
        
        
        def main(myTimer: func.TimerRequest, signalRMessages: func.Out[str]) -> None:
            headers = {'User-Agent': 'serverless'}
            res = requests.get('https://api.github.com/repos/azure/azure-signalr', headers=headers)
            jres = res.json()
        
            signalRMessages.set(json.dumps({
                'target': 'newMessage',
                'arguments': [ 'Current star count of https://github.com/Azure/azure-signalr is: ' + str(jres['stargazers_count']) ]
            }))
        ```

3. L’interface cliente de cet exemple est une page web. Comme nous avons lu le contenu HTML de `content/index.html` dans la fonction `index`, créez un fichier `index.html` dans le répertoire `content`. Ensuite, copiez le contenu suivant.

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
    
4. C’est presque terminé. La dernière étape consiste à définir une chaîne de connexion de SignalR Service sur les paramètres de fonction Azure.

    1. Dans le navigateur dans lequel le portail Azure est ouvert, vérifiez que l’instance du service SignalR que vous avez déployée précédemment a bien été créée en recherchant son nom dans la zone de recherche en haut du portail. Sélectionnez l’instance pour l’ouvrir.

        ![Rechercher l’instance du service SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. Sélectionnez **Clés** pour afficher les chaînes de connexion de l’instance du service SignalR.
    
        ![Capture d’écran qui met en évidence la chaîne de connexion principale.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. Copiez la chaîne de connexion principale. Ensuite, exécutez la commande ci-dessous.
    
        ```bash
        func settings add AzureSignalRConnectionString '<signalr-connection-string>'
        ```
    
5. Exécutez la fonction Azure en local :

    ```bash
    func start
    ```

    Une fois que la fonction Azure s’exécute localement, utilisez votre navigateur pour accéder à l’adresse `http://localhost:7071/api/index` et voir le nombre d’étoiles actuel. Si vous attribuez ou retirez des étoiles dans GitHub, le nombre d’étoiles est actualisé toutes les quelques secondes.

    > [!NOTE]
    > La liaison SignalR a besoin du Stockage Azure, mais vous pouvez utiliser l’émulateur de stockage local quand la fonction est exécutée localement.
    > Si vous rencontrez des erreurs telles que `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, vous devez télécharger et activer l’[émulateur de stockage](../storage/common/storage-use-emulator.md).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez généré et exécuté une application serverless en temps réel en local. Découvrez comment utiliser les liaisons SignalR Service pour Azure Functions.
Ensuite, découvrez comment permettre une communication bidirectionnelle entre les clients et Azure Function grâce à SignalR Service.

> [!div class="nextstepaction"]
> [Liaisons de service SignalR pour Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Communication bidirectionnelle dans un scénario serverless](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Déployer Azure Functions avec VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)