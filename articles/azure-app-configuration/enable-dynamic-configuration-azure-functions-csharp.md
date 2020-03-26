---
title: Tutoriel pour utiliser la configuration dynamique Azure App Configuration dans une application Azure Functions | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74187293"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application Azure Functions

Le fournisseur de configuration .NET Standard App Configuration prend en charge la mise en cache et l’actualisation de la configuration pilotée dynamiquement par l’activité des applications. Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application Azure Functions mentionnée dans les guides de démarrage rapide. Avant de continuer, terminez d’abord [Créer une application Azure Functions avec Azure App Configuration](./quickstart-azure-functions-csharp.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre application Azure Functions pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin App Configuration.
> * Injecter la configuration la plus récente dans vos appels Azure Functions.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) avec la charge de travail **Développement Azure**
- [Outils Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Terminez le guide de démarrage rapide [Créer une application Azure Functions avec Azure App Configuration](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration

1. Ouvrez *Function1.cs*. En plus de la propriété `static``Configuration`, ajoutez une nouvelle propriété `static``ConfigurationRefresher` pour conserver une instance singleton de `IConfigurationRefresher` qui sera utilisée pour signaler les mises à jour de configuration pendant les appels Functions ultérieurement.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Mettez à jour le constructeur et utilisez la méthode `ConfigureRefresh` pour spécifier le paramètre à actualiser à partir du magasin App Configuration. Une instance de `IConfigurationRefresher` est récupérée à l’aide de la méthode `GetRefresher`. Si vous le souhaitez, nous remplaçons également les 30 secondes par défaut de la fenêtre de délai d’expiration du cache de configuration par 1 minute.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Mettez à jour la méthode `Run` et indiquez d’actualiser la configuration à l’aide de la méthode `Refresh` au début de l’appel Functions. Aucune opération n’est effectuée si la fenêtre de délai d’expiration du cache n’est pas atteinte. Supprimez l’opérateur `await` si vous préférez que la configuration soit actualisée sans blocage.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Tester la fonction en local

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pour tester votre fonction, appuyez sur F5. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer **Azure Functions Core (CLI) Tools**. Vous devrez peut-être aussi activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

3. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.

    ![Démarrage rapide du débogage de fonctions dans Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. La capture d’écran suivante du navigateur montre la réponse retournée par la fonction, suite à la requête GET locale.

    ![Démarrage rapide du lancement de fonctions local](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

6. Sélectionnez **Explorateur de configuration**, puis mettez à jour les valeurs de la clé suivante :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Données issues d’Azure App Configuration - Mise à jour |

7. Actualisez le navigateur à plusieurs reprises. Lorsque le paramètre mis en cache expire au bout d’une minute, la page affiche la réponse de l’appel Functions avec la valeur mise à jour.

    ![Démarrage rapide de l’actualisation de fonctions en local](./media/quickstarts/dotnet-core-function-refresh-local.png)

L’exemple de code utilisé dans ce tutoriel peut être téléchargé à partir du [dépôt GitHub App Configuration](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application Azure Functions d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
