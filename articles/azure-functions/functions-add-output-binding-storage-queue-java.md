---
title: Connecter votre fonction Java à Stockage Azure
description: Découvrez comment connecter une fonction Java déclenchée par HTTP à Stockage Azure à l’aide d’une liaison de sortie de stockage de file d’attente.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78272802"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Connecter votre fonction Java à Stockage Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Cet article vous montre comment intégrer la fonction que vous avez créée dans l’[article du guide de démarrage rapide précédent](functions-create-first-java-maven.md) à une file d’attente Stockage Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

La plupart des liaisons requièrent une chaîne de connexion stockée que Functions utilise pour accéder au service lié. Pour faciliter cette connexion, vous utilisez le compte de stockage que vous avez créé avec votre application de fonction. La connexion à ce compte est déjà stockée dans un paramètre d’application nommé `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer cet article, suivez les étapes de la [partie 1 du guide de démarrage rapide Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Télécharger les paramètres de l’application de fonction

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Vous pouvez maintenant ajouter la liaison de sortie de stockage à votre projet.

## <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Dans un projet Java, les liaisons sont définies comme annotations de liaison sur la méthode de fonction. Le fichier *function.json* est ensuite généré automatiquement en fonction de ces annotations.

Accédez à l’emplacement de votre code de fonction sous _src/main/java_, ouvrez le fichier projet *Function.java*, puis ajoutez le paramètre suivant à la définition de la méthode `run` :

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Le paramètre `msg` est un type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), qui représente une collection de chaînes écrites en tant que messages à une liaison de sortie quand la fonction se termine. Dans ce cas, la sortie est une file d’attente de stockage nommée `outqueue`. La chaîne de connexion pour le compte de stockage est définie par la méthode `connection`. Au lieu de passer la chaîne de connexion proprement dite, vous passez le paramètre d’application qui contient la chaîne de connexion du compte de stockage.

La définition de la méthode `run` doit maintenant ressembler à l’exemple suivant :  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Ajouter le code qui utilise la liaison de sortie

À présent, vous pouvez utiliser le nouveau paramètre `msg` pour écrire dans la liaison de sortie à partir de votre code de fonction. Ajoutez la ligne de code suivante avant la réponse de réussite pour ajouter la valeur de `name` à la liaison de sortie `msg`.

```java
msg.setValue(name);
```

Lorsque vous utilisez une liaison de sortie, vous n’avez pas besoin de recourir au code du Kit de développement logiciel (SDK) Stockage Azure pour l’authentification, l’obtention d’une référence de file d’attente ou l’écriture de données. La liaison de sortie de file d’attente et le runtime Functions effectuent ces tâches.

Votre méthode `run` doit maintenant ressembler à l’exemple suivant :

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Mettre à jour les tests

Étant donné que l’archétype crée également un ensemble de tests, vous devez mettre à jour ces tests pour gérer le nouveau paramètre `msg` dans la signature de méthode `run`.  

Accédez à l’emplacement de votre code de test sous _src/test/Java_, ouvrez le fichier projet *Function.java* et remplacez la ligne de code sous `//Invoke` par le code suivant.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Vous êtes maintenant prêt à essayer la nouvelle liaison de sortie localement.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Comme auparavant, utilisez la commande suivante pour générer le projet et démarrer le runtime Functions localement :

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

> [!NOTE]  
> Comme vous avez activé des bundles d’extension dans le fichier host.json, l’[extension de liaison de stockage](functions-bindings-storage-blob.md#add-to-your-functions-app) a été téléchargée et installée lors du démarrage, avec les autres extensions de liaison Microsoft.

Comme précédemment, déclenchez la fonction à partir de la ligne de commande en utilisant cURL dans une nouvelle fenêtre de terminal :

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Cette fois, la liaison de sortie crée également une file d’attente nommée `outqueue` dans votre compte de stockage et ajoute un message avec cette même chaîne.

Ensuite, vous utilisez l’interface de ligne de commande Azure pour afficher la nouvelle file d’attente et vérifier qu’un message a été ajouté. Vous pouvez également afficher votre file d’attente en utilisant l’[Explorateur Stockage Azure][Azure Storage Explorer] ou sur le [portail Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Redéployer le projet 

Pour mettre à jour votre application publiée, réexécutez la commande suivante :  

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Là encore, vous pouvez utiliser cURL pour tester la fonction déployée. Comme précédemment, transmettez la valeur `AzureFunctions` dans le corps de la requête POST à l’URL, comme dans l’exemple suivant :

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Vous pouvez [réexaminer le message de file d’attente de stockage](#query-the-storage-queue) pour vérifier que la liaison de sortie génère un nouveau message dans la file d’attente, comme prévu.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez mis à jour votre fonction déclenchée via HTTP pour écrire des données dans une file d’attente de stockage. Pour en savoir plus sur le développement de fonctions Azure Functions avec Java, consultez le [Guide des développeurs Java sur Azure Functions](functions-reference-java.md) et [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md). Pour obtenir des exemples de projets Functions complets en Java, consultez les [exemples Functions en Java](/samples/browse/?products=azure-functions&languages=Java). 

Ensuite, vous devez activer la supervision Application Insights pour votre application de fonction :

> [!div class="nextstepaction"]
> [Activer l’intégration à Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
