---
title: Utiliser des identités managées pour accéder à App Configuration
titleSuffix: Azure App Configuration
description: S’authentifier auprès d’Azure App Configuration à l’aide d’identités managées
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: ff7c2b6ced87c8254283923a9163e51f06ae6ef6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114298171"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Utiliser des identités managées pour accéder à App Configuration

Les [identités managées](../active-directory/managed-identities-azure-resources/overview.md) Azure Active Directory simplifient la gestion des secrets pour votre application cloud. Avec une identité managée, votre code peut utiliser le principal de service qui a été créé pour le service Azure sur lequel il s’exécute. Vous utilisez une identité managée à la place d’une information d’identification distincte stockée dans Azure Key Vault ou une chaîne de connexion locale.

Azure App Configuration et ses bibliothèques clientes .NET Core, .NET Framework et Java Spring sont fournis avec la prise en charge de l’identité managée qui leur est intégrée. Bien que vous ne soyez pas obligé de l’utiliser, l’identité managée élimine le besoin d’un jeton d’accès contenant des secrets. Votre code peut accéder au magasin App Configuration à l’aide du point de terminaison de service uniquement. Vous pouvez incorporer cette URL directement dans votre code sans exposer les secrets.

Cet article montre comment tirer parti de l’identité managée pour accéder à App Configuration. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, [créez une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

> [!IMPORTANT]
> L’identité managée ne peut pas être utilisée pour authentifier des applications en cours d’exécution localement. Votre application doit être déployée sur un service Azure qui prend en charge l’identité managée. Cet article utilise Azure App Service à titre d’exemple, mais le même concept s’applique à tout autre service Azure qui prend en charge l’identité managée, par exemple, [Azure Kubernetes Service](../aks/use-azure-ad-pod-identity.md), [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) et [Azure Container Instances](../container-instances/container-instances-managed-identity.md). Si votre charge de travail est hébergée dans l’un de ces services, vous pouvez également tirer parti de la prise en charge des identités managées de ce service.

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Accorder à une identité managée l’accès à App Configuration.
> * Configurer votre application pour utiliser une identité managée lors de la connexion à App Configuration.


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin de :

* [SDK .NET Core](https://dotnet.microsoft.com/download).
* [Azure Cloud Shell configuré](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Ajouter une identité managée

Pour configurer une identité managée dans le portail, créez d’abord une application, puis activez la fonctionnalité.

1. Accédez à votre ressource App Services dans le [Portail Azure](https://portal.azure.com). Si vous n’avez pas de ressource de App Services existante à utiliser, créez-en une. 

1. Faites défiler l’écran jusqu’au groupe **Paramètres** dans le volet de gauche, puis sélectionnez **Identité**.

1. Sous l’onglet **Affecté(e) par le système**, définissez **État** sur **Activé**, puis sélectionnez **Enregistrer**.

1. Répondez **Oui** lorsque vous êtes invité à activer l’identité managée affectée par le système.

    ![Définir une identité managée dans App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Accorder l’accès à App Configuration

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis sélectionnez le magasin App Configuration que vous avez créé dans le guide de démarrage rapide.

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sous l’onglet **Vérifier l’accès**, sélectionnez **Ajouter** dans l’interface utilisateur de carte **Ajouter une attribution de rôle**.

1. Sous **Rôle**, sélectionnez **Lecteur de données de l’App Configuration**. Sous **Attribuer l’accès à**, sélectionnez **App Service** sous **Identité managée affectée par le système**.

1. Sous **Abonnement**, sélectionnez votre abonnement Azure. Sélectionnez la ressource App Service de votre application.

1. Sélectionnez **Enregistrer**.

    ![Ajouter une identité managée](./media/add-managed-identity.png)


## <a name="use-a-managed-identity"></a>Utiliser une identité managée

1. Ajoutez une référence au package *Azure.Identity* :

    ```bash
    dotnet add package Azure.Identity
    ```

1. Recherchez le point de terminaison de votre magasin App Configuration. Cette URL est indiquée sous l’onglet **Clés d’accès** pour le magasin du portail Azure.

1. Ouvrez *appsettings.json*, puis ajoutez le script suivant. Remplacez *\<service_endpoint>* , crochets compris, par l’URL de votre magasin App Configuration.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Ouvrez *Program.cs* et ajoutez une référence aux espaces de noms `Azure.Identity` et `Microsoft.Azure.Services.AppAuthentication` :

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Si vous souhaitez accéder uniquement aux valeurs stockées directement dans App Configuration, mettez à jour la méthode `CreateWebHostBuilder` en remplaçant la méthode `config.AddAzureAppConfiguration()` (disponible dans le package `Microsoft.Azure.AppConfiguration.AspNetCore`).

    > [!IMPORTANT]
    > `CreateHostBuilder` remplace `CreateWebHostBuilder` dans .NET Core 3.0.  Sélectionnez la syntaxe appropriée en fonction de votre environnement.

    ### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ---

    > [!NOTE]
    > Si vous souhaitez utiliser une **identité managée affectée par l’utilisateur**, veillez à spécifier le cliendId lors de la création des [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential).
    >```csharp
    >config.AddAzureAppConfiguration(options =>
    >       {
    >           options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential("<your_clientId>"))
    >        });
    >```
    >Comme expliqué dans les [Questions fréquentes (FAQ) relatives aux identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/known-issues.md), il existe un moyen par défaut de savoir quelle identité managée est utilisée. Dans ce cas, la bibliothèque d’identités Azure vous oblige à spécifier l’identité souhaitée afin d’éviter tout problème d’exécution à l’avenir (par exemple, si une nouvelle identité managée affectée par l’utilisateur est ajoutée ou si l’identité managée affectée par le système est activée). Par conséquent, vous devez spécifier le clientId même si une seule identité managée affectée par l’utilisateur est définie et qu’il n’existe aucune identité managée affectée par le système.
 
    

## <a name="deploy-your-application"></a>Déployer votre application

L’utilisation des identités gérées vous oblige à déployer votre application sur un service Azure. Les identités gérées ne peuvent pas être utilisées pour l’authentification des applications exécutées localement. Pour déployer l’application .NET Core que vous avez créée à l’aide du démarrage rapide [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md) et la modifier pour utiliser des identités gérées, suivez les instructions de la section [Publier votre application Web](../app-service/quickstart-dotnetcore.md?pivots=development-environment-vs&tabs=netcore31#publish-your-web-app).

En plus de App Service, de nombreux autres services Azure prennent en charge les identités gérées. Pour plus d'informations, consultez [Services prenant en charge les identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez ajouté une identité managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Exemples d’interface de ligne de commande](./cli-samples.md)