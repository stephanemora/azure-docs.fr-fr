---
title: Démarrage rapide d’Azure App Configuration avec Azure Functions | Microsoft Docs
description: Guide de démarrage rapide pour utiliser Azure App Configuration avec Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5f28e213a5f824562df62a05b98f0f92f71bc591
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957434"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Démarrage rapide : Créer une fonction Azure avec App Configuration

Azure App Configuration est un service de configuration managée dans Azure. Il vous permet de stocker et gérer facilement tous les paramètres de votre application à un seul endroit, indépendamment de votre code. Ce guide de démarrage rapide vous montre comment intégrer le service à une fonction Azure. 

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce démarrage rapide. Toutefois, [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

![Démarrage rapide complet local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, installez [Visual Studio 2017](https://visualstudio.microsoft.com/vs) (et vérifiez que la charge de travail de **développement Azure** est également installée) et les [derniers outils Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Créer un magasin de configuration d’application

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Créer une application de fonction

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Se connecter au magasin de configuration d’application

1. Ouvrez *Function1.cs* et ajoutez une référence au fournisseur de configuration App Configuration .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Mettez à jour la méthode `Run` pour utiliser App Configuration en appelant `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Tester la fonction en local

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification prenne effet :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pour tester votre fonction, appuyez sur **F5**. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer **Azure Functions Core (CLI) Tools**. Vous devrez peut-être activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

3. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.

    ![Démarrage rapide du débogage de fonctions dans Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande locale GET :

    ![Démarrage rapide du lancement de fonctions local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin de configuration d’application et vous l’avez utilisé avec une fonction Azure. Pour en savoir plus sur l’utilisation d’App Configuration, passez au tutoriel suivant pour découvrir l’authentification.

> [!div class="nextstepaction"]
> [Identités managées pour l’intégration des ressources Azure](./integrate-azure-managed-service-identity.md)
