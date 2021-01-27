---
title: Démarrage rapide d’Azure App Configuration avec Azure Functions | Microsoft Docs
description: Dans ce guide de démarrage rapide, créez une application Azure Functions avec Azure App Configuration et C#. Créez et connectez-vous à un magasin App Configuration. Testez la fonction localement.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724235"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application Azure Functions avec Azure App Configuration

Dans ce guide de démarrage rapide, vous allez intégrer le service Azure App Configuration dans une application Azure Functions afin de centraliser le stockage et la gestion de tous vos paramètres d’application en dehors de votre code.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) avec la charge de travail **Développement Azure**
- [Outils Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Sélectionnez **Explorateur de configurations** >  **+ Créer** > **Clé-valeur** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

8. Sélectionnez **Appliquer**.

## <a name="create-a-functions-app"></a>Créer une application Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration
Ce projet utilise l’[injection de dépendances dans .NET Azure Functions](../azure-functions/functions-dotnet-dependency-injection.md) et ajoute Azure App Configuration comme source de configuration supplémentaire.

1. Cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, recherchez les packages NuGet suivants et ajoutez-les à votre projet.
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) version 4.1.0 ou ultérieure
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) version 1.1.0 ou ultérieure 

2. Ajoutez un nouveau fichier, *Startup.cs*, avec le code suivant. Il définit une classe nommée `Startup` qui implémente la classe abstraite `FunctionsStartup`. Un attribut d’assembly est utilisé pour spécifier le nom du type utilisé durant le démarrage d’Azure Functions.

    La méthode `ConfigureAppConfiguration` est substituée et le fournisseur Azure App Configuration est ajouté comme source de configuration supplémentaire en appelant `AddAzureAppConfiguration()`. La méthode `Configure` reste vide, car vous n’avez pas besoin d’inscrire de services à ce stade.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. Ouvrez *Function1.cs* et ajoutez l’espace de noms suivant.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Ajoutez un constructeur utilisé pour obtenir une instance de `IConfiguration` par le biais de l’injection de dépendances.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. Mettez à jour la méthode `Run` pour lire les valeurs de la configuration.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   La classe `Function1` et la méthode `Run` ne doivent pas être statiques. Supprimez le modificateur `static` s’il a été généré automatiquement.

## <a name="test-the-function-locally"></a>Tester la fonction en local

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin App Configuration. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Appuyez sur F5 pour tester votre fonction. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer **Azure Functions Core (CLI) Tools**. Vous devrez peut-être aussi activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

3. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.

    ![Démarrage rapide du débogage de fonctions dans Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. La capture d’écran suivante du navigateur montre la réponse retournée par la fonction, suite à la requête GET locale.

    ![Démarrage rapide du lancement de fonctions local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin App Configuration et l’avez utilisé avec une application Azure Functions par l’intermédiaire du [fournisseur App Configuration](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Pour savoir comment mettre à jour votre application Azure Functions afin d’actualiser dynamiquement la configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Activer la configuration dynamique dans Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)