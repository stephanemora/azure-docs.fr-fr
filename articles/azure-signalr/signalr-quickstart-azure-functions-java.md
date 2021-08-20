---
title: Utiliser Java pour créer une salle de conversation avec Azure Functions et le service SignalR
description: Démarrage rapide montrant comment utiliser Azure SignalR Service et Azure Functions pour créer une application indiquant le nombre d’étoiles GitHub en utilisant Java.
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: fdcc8b9355556804b2f13fccd206eb13ac7c0cb6
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112462052"
---
# <a name="quickstart-use-java-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>Démarrage rapide : Utiliser Java pour créer une application qui indique le nombre d’étoiles GitHub avec Azure Functions et SignalR Service

Azure SignalR Service vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application, et Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructure. Dans ce démarrage rapide, découvrez comment utiliser SignalR Service et Azure Functions pour créer une application serverless avec Java afin de diffuser des messages aux clients.

> [!NOTE]
> Tous les codes mentionnés dans l’article sont accessibles sur le site [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/java).

## <a name="prerequisites"></a>Prérequis

- Un éditeur de code, comme [Visual Studio Code](https://code.visualstudio.com/).
- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Sert à exécuter les applications Azure Functions localement.

   > [!NOTE]
   > Les liaisons SignalR Service nécessaires dans Java sont uniquement prises en charge dans Azure Function Core Tools version 2.4.419 (version d’hôte 2.0.12332) ou supérieure.

   > [!NOTE]
   > Pour installer des extensions, Azure Functions Core Tools nécessite l’installation du kit [SDK .NET Core](https://dotnet.microsoft.com/download). Toutefois, aucune connaissance de .NET n’est requise pour générer des applications de fonction Azure JavaScript.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 11
- [Apache Maven](https://maven.apache.org), version 3.0 ou ultérieure

> [!NOTE]
> Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjava).


## <a name="configure-and-run-the-azure-function-app"></a>Configurer et exécuter l’application de fonction Azure

1. Vérifiez qu’Azure Functions Core Tools, Java (version 11 dans l’exemple) et Maven sont installés.
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=11
    ```

    Maven vous demande d’entrer les valeurs nécessaires pour terminer la génération du projet. Vous pouvez fournir les valeurs suivantes.

    | Prompt | Valeur | Description |
    | ------ | ----- | ----------- |
    | **groupId** | `com.signalr` | Valeur qui identifie de façon unique votre projet parmi tous les projets, avec respect des [règles de nommage de package](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pour Java. |
    | **artifactId** | `java` | Valeur qui correspond au nom du fichier jar, sans numéro de version. |
    | **version** | `1.0-SNAPSHOT` | Choisissez la valeur par défaut. |
    | **package** | `com.signalr` | Valeur qui correspond au package Java pour le code de fonction généré. Utilisez la valeur par défaut. |   

2. Après avoir initialisé un projet, accédez au dossier `src/main/java/com/signalr` et copiez les codes suivants dans `Function.java`.

    ```java
    package com.signalr;
  
    import com.google.gson.Gson;
    import com.microsoft.azure.functions.ExecutionContext;
    import com.microsoft.azure.functions.HttpMethod;
    import com.microsoft.azure.functions.HttpRequestMessage;
    import com.microsoft.azure.functions.HttpResponseMessage;
    import com.microsoft.azure.functions.HttpStatus;
    import com.microsoft.azure.functions.annotation.AuthorizationLevel;
    import com.microsoft.azure.functions.annotation.FunctionName;
    import com.microsoft.azure.functions.annotation.HttpTrigger;
    import com.microsoft.azure.functions.annotation.TimerTrigger;
    import com.microsoft.azure.functions.signalr.*;
    import com.microsoft.azure.functions.signalr.annotation.*;
    
    import org.apache.commons.io.IOUtils;
    
    
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.HttpResponse.BodyHandlers;
    import java.nio.charset.StandardCharsets;
    import java.util.Optional;
    
    public class Function {
        @FunctionName("index")
        public HttpResponseMessage run(
                @HttpTrigger(
                    name = "req",
                    methods = {HttpMethod.GET},
                    authLevel = AuthorizationLevel.ANONYMOUS)HttpRequestMessage<Optional<String>> request,
                final ExecutionContext context) throws IOException {
            
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("content/index.html");
            String text = IOUtils.toString(inputStream, StandardCharsets.UTF_8.name());
            return request.createResponseBuilder(HttpStatus.OK).header("Content-Type", "text/html").body(text).build();
        }
  
        @FunctionName("negotiate")
        public SignalRConnectionInfo negotiate(
                @HttpTrigger(
                    name = "req",
                    methods = { HttpMethod.POST },
                    authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
                @SignalRConnectionInfoInput(
                    name = "connectionInfo",
                    hubName = "serverless") SignalRConnectionInfo connectionInfo) {
                    
            return connectionInfo;
        }
    
        @FunctionName("broadcast")
        @SignalROutput(name = "$return", hubName = "serverless")
        public SignalRMessage broadcast(
            @TimerTrigger(name = "timeTrigger", schedule = "*/5 * * * * *") String timerInfo) throws IOException, InterruptedException {
            
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest req = HttpRequest.newBuilder().uri(URI.create("https://api.github.com/repos/azure/azure-signalr")).header("User-Agent", "serverless").build();
            HttpResponse<String> res = client.send(req, BodyHandlers.ofString());
            Gson gson = new Gson();
            GitResult result = gson.fromJson(res.body(), GitResult.class);
            return new SignalRMessage("newMessage", "Current start count of https://github.com/Azure/azure-signalr is:".concat(result.stargazers_count));
        }
    
        class GitResult {
            public String stargazers_count;
        }
    }
    ```

3. Certaines dépendances doivent être ajoutées. Par conséquent, ouvrez `pom.xml` et ajoutez certaines dépendances utilisées dans les codes.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.functions</groupId>
        <artifactId>azure-functions-java-library-signalr</artifactId>
        <version>1.0.0</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.4</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.7</version>
    </dependency>
    ```

4. L’interface client de cet exemple est une page web. En considérant que nous lisons le contenu HTML de `content/index.html` dans la fonction `index`, créez un nouveau fichier `content/index.html` dans le répertoire `resources`. L’arborescence de votre répertoire doit ressembler à ceci.
    
    ```
    FunctionsProject
     | - src
     | | - main
     | | | - java
     | | | | - com
     | | | | | - signalr 
     | | | | | | - Function.java
     | | | - resources
     | | | | - content
     | | | | | - index.html
     | - pom.xml
     | - host.json
     | - local.settings.json
    ```

    Ouvrez le fichier `index.html` et copiez le contenu suivant.

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

5. C’est presque terminé. La dernière étape consiste à définir une chaîne de connexion de SignalR Service dans les paramètres d’Azure Functions.

    1. Dans le navigateur dans lequel le portail Azure est ouvert, vérifiez que l’instance du service SignalR que vous avez déployée précédemment a bien été créée en recherchant son nom dans la zone de recherche en haut du portail. Sélectionnez l’instance pour l’ouvrir.

        ![Rechercher l’instance du service SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. Sélectionnez **Clés** pour afficher les chaînes de connexion de l’instance du service SignalR.
    
        ![Capture d’écran qui met en évidence la chaîne de connexion principale.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. Copiez la chaîne de connexion principale. Puis exécutez la commande ci-dessous.
    
        ```bash
        func settings add AzureSignalRConnectionString '<signalr-connection-string>'
        # Also we need to set AzureWebJobsStorage as Azure Function's requirement
        func settings add AzureWebJobsStorage 'UseDevelopmentStorage=true'
        ```
    
6. Exécutez la fonction Azure en local :

    ```bash
    mvn clean package
    mvn azure-functions:run
    ```

    Après l’exécution locale de la fonction Azure, utilisez votre navigateur pour accéder à l’adresse `http://localhost:7071/api/index` et voir le nombre actuel d’étoiles. Si vous ajoutez ou supprimez une étoile sur le site GitHub, le nombre d’étoiles sera actualisé toutes les quelques secondes.

    > [!NOTE]
    > La liaison SignalR nécessite Stockage Azure, mais vous pouvez utiliser l’émulateur de stockage local lorsque la fonction est exécutée localement.
    > Si vous rencontrez des erreurs telles que `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, vous devez télécharger et activer l’[émulateur de stockage](../storage/common/storage-use-emulator.md).
    
Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjava).


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Vous rencontrez des problèmes ? Essayez le [guide de résolution des problèmes](signalr-howto-troubleshoot-guide.md) ou [faites-le nous savoir](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez généré et exécuté une application serverless en temps réel en local. Découvrez comment utiliser les liaisons SignalR Service pour Azure Functions.
Ensuite, découvrez comment permettre une communication bidirectionnelle entre les clients et Azure Function grâce à SignalR Service.

> [!div class="nextstepaction"]
> [Liaisons de service SignalR pour Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Communication bidirectionnelle dans Serverless](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Créer votre première fonction avec Java et Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
