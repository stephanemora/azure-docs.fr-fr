---
title: Déclencheur d’objet blob Event Grid d’Azure Functions
description: Apprendre à configurer et à déboguer avec le déclencheur d’objet blob Event Grid
author: cachai2
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: cachai
ms.openlocfilehash: 259928cbfc046930d82bc3462db6d8aad4cd6b06
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124992"
---
# <a name="azure-function-event-grid-blob-trigger"></a>Déclencheur d’objet blob Event Grid d’Azure Functions

Cet article montre comment déboguer et déployer une fonction déclenchée par un objet blob Event Grid local qui gère les événements déclenchés par un compte de stockage.

> [!NOTE]
> Le déclencheur d’objet blob Event Grid est en préversion.

## <a name="prerequisites"></a>Prérequis

- Créer ou utiliser une application de fonction existante
- Créer ou utiliser un compte de stockage existant
- Avoir la version 5.0+ de [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2) installée
- Télécharger [ngrok](https://ngrok.com/) pour permettre à Azure d’appeler une fonction locale

## <a name="create-a-new-function"></a>Créer une fonction

1. Ouvrez votre application de fonction dans Visual Studio.

1. **Appuyez sur F1** pour créer une fonction de déclencheur d’objet blob. Veillez à utiliser la chaîne de connexion pour votre compte de stockage.

1. L’URL par défaut de votre déclencheur d’objet blob Event Grid est la suivante :

    # <a name="c"></a>[C#](#tab/csharp)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName={functionname}
    ```

    # <a name="python"></a>[Python](#tab/python)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    # <a name="java"></a>[Java](#tab/java)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    ---

    Notez le nom de votre application de fonction et le fait que le type de déclencheur est un déclencheur d’objet blob, indiqué par `blobs` dans l’URL. Cela sera nécessaire lors de la configuration des points de terminaison ultérieurement dans ce guide pratique.

1. Une fois la fonction créée, ajoutez le paramètre source Event Grid.

    # <a name="c"></a>[C#](#tab/csharp)
    Ajoutez **Source = BlobTriggerSource.EventGrid** aux paramètres de la fonction.
    
    ```csharp
    [FunctionName("BlobTriggerCSharp")]
    public static void Run([BlobTrigger("samples-workitems/{name}", Source = BlobTriggerSource.EventGrid, Connection = "connection")]Stream myBlob, string name, ILogger log)
    {
        log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
    }
    ```

    # <a name="python"></a>[Python](#tab/python)
    Ajoutez **"source": "EventGrid"** aux données de liaison de function.json.
    
    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "name": "myblob",
          "type": "blobTrigger",
          "direction": "in",
          "path": "samples-workitems/{name}",
          "source": "EventGrid",
          "connection": "MyStorageAccountConnectionString"
        }
      ]
    }
    ```

    # <a name="java"></a>[Java](#tab/java)
    **Appuyez sur F5** pour générer la fonction. Une fois la génération terminée, ajoutez **"source": "EventGrid"** aux données de liaison **function.json**.
    
    ```json
    {
      "scriptFile" : "../java-1.0-SNAPSHOT.jar",
      "entryPoint" : "com.function.{MyFunctionName}.run",
      "bindings" : [ {
        "type" : "blobTrigger",
        "direction" : "in",
        "name" : "content",
        "path" : "samples-workitems/{name}",
        "dataType" : "binary",
        "source": "EventGrid",
        "connection" : "MyStorageAccountConnectionString"
       } ]
    }
    ```

    ---

1. Définissez un point d’arrêt dans votre fonction sur la ligne qui gère la journalisation.

1. Démarrez une session de débogage.

    # <a name="c"></a>[C#](#tab/csharp)
    **Appuyez sur F5** pour démarrer une session de débogage.

    # <a name="python"></a>[Python](#tab/python)
    **Appuyez sur F5** pour démarrer une session de débogage.

    # <a name="java"></a>[Java](#tab/java)
    Ouvrez un nouveau terminal et exécutez la commande mvn ci-dessous pour démarrer la session de débogage.

    ```bash
    mvn azure-functions:run
    ```

    ---

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>Déboguer la fonction
Quand le Déclencheur d’objet blob reconnaît qu’un nouveau fichier est chargé dans le conteneur de stockage, le point d’arrêt est atteint dans votre fonction locale.

## <a name="deployment"></a>Déploiement

Lorsque vous déployez l’application de fonction dans Azure, mettez à jour le point de terminaison du webhook de votre point de terminaison local vers le point de terminaison de l’application déployée. Pour mettre à jour un point de terminaison, suivez les étapes de la section [Ajouter un événement de stockage](#add-a-storage-event) et utilisez la section ci-dessous pour l’URL du webhook à l’étape 5. `<BLOB-EXTENSION-KEY>` se trouve dans la section **Clés d’application** du menu de gauche de votre **application de fonction**.

# <a name="c"></a>[C#](#tab/csharp)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="python"></a>[Python](#tab/python)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="java"></a>[Java](#tab/java)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour nettoyer les ressources créées dans cet article, supprimez l’abonnement Event Grid que vous avez créé dans ce tutoriel.

## <a name="next-steps"></a>Étapes suivantes

- [Automatiser le redimensionnement des images chargées à l’aide d’Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Déclencheur Event Grid pour Azure Functions](./functions-bindings-event-grid.md)