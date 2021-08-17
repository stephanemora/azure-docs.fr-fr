---
title: Tutoriel concernant l’utilisation de références Key Vault avec Azure App Configuration dans une application ASP.NET Core | Microsoft Docs
description: Dans ce tutoriel, vous apprenez à utiliser les références Key Vault avec Azure App Configuration depuis une application ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: e02ac9d6abd3358218f268fb3da1e99b90fac7c5
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568079"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutoriel : Utiliser des références Key Vault dans une application ASP.NET Core

Dans ce tutoriel, vous apprenez à utiliser le service Azure App Configuration avec Azure Key Vault. App Configuration et Key Vault sont des services complémentaires utilisés côte à côte dans la plupart des déploiements d’applications.

App Configuration vous aide à utiliser ces services ensemble en créant des clés qui référencent des valeurs stockées dans Key Vault. Lorsque le service App Configuration crée de telles clés, il stocke les URI des valeurs Key Vault plutôt que les valeurs elles-mêmes.

Votre application utilise le fournisseur client de App Configuration pour récupérer les références Key Vault, tout comme pour toutes les autres clés stockées dans App Configuration. Dans ce cas, les valeurs stockées dans App Configuration sont des URI qui référencent les valeurs dans le coffre de clés. Elles ne sont pas des valeurs de coffre de clés ou des informations d’identification. Étant donné que le fournisseur client reconnaît les clés comme des références Key Vault, il utilise Key Vault pour récupérer leurs valeurs.

Votre application est chargée de s’authentifier correctement auprès d’App Configuration et auprès de Key Vault. Les deux services ne communiquent pas directement.

Ce tutoriel vous montre comment implémenter des références Key Vault dans votre code. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. Par exemple, [Visual Studio Code](https://code.visualstudio.com/) est un éditeur de code multiplateforme qui est disponible pour les systèmes d’exploitation Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une clé App Configuration qui référence une valeur stockée dans Key Vault.
> * Accéder à la valeur de cette clé à partir d’une application web ASP.NET Core.

## <a name="prerequisites"></a>Prérequis

Avant d’effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Création d'un coffre

1. Sélectionnez l’option **Créer une ressource** dans le coin supérieur gauche du Portail Azure :

    ![La capture d’écran montre l’option Créer une ressource dans le portail Azure.](./media/quickstarts/search-services.png)
1. Dans la zone de recherche, tapez **Coffre de clés** et sélectionnez **Coffre de clés** dans la liste déroulante.
1. Dans la liste des résultats, sélectionnez **Coffres de clés** sur la gauche.
1. Dans **Coffres de clés**, sélectionnez **Ajouter**.
1. Sur la droite, dans **Créer un coffre de clés**, renseignez les informations suivantes :
    - Sélectionnez **Abonnement** pour choisir un abonnement.
    - Dans **Groupe de ressources**, saisissez le nom d’un groupe de ressources existant ou sélectionnez **Créer nouveau** et entrez un nom de groupe de ressources.
    - Dans **Nom du coffre de clés**, un nom unique est requis.
    - Dans la liste déroulante **Région**, choisissez un emplacement.
1. Conservez les valeurs par défaut des autres options **Création d’un coffre de clés**.
1. Cliquez sur **Vérifier + créer**.
1. Le système valide et affiche les données que vous avez entrées. Cliquez sur **Créer**.

À ce stade, votre compte Azure est le seul autorisé à accéder à ce nouveau coffre.


## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés

Pour ajouter un secret au coffre, vous n’avez qu’à effectuer deux autres étapes. Dans ce cas, ajoutez un message que vous pouvez utiliser pour tester la récupération de Key Vault. Le message est appelé **Message** et vous stockez la valeur « Hello from Key Vault » dedans.

1. Depuis les pages des propriétés Key Vault, sélectionnez **Secrets**.
1. Sélectionnez **Générer/Importer**.
1. Dans le volet **Créer un secret**, saisissez les valeurs suivantes :
    - **Options de chargement** : Entrez **Manuel**.
    - **Name** : Entrez **Message**.
    - **Valeur** : Entrez **Hello from Key Vault**.
1. Conservez les valeurs par défaut des autres propriétés **Créer un secret**.
1. Sélectionnez **Create** (Créer).

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Ajouter une référence Key Vault à App Configuration

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

1. Sélectionnez **Explorateur de configuration**.

1. Sélectionnez **+ Créer** > **Référence Key Vault**, puis choisissez les valeurs suivantes :
    - **Clé** : Sélectionnez **TestApp:Settings:KeyVaultMessage**.
    - **Étiquette** : Laissez cette valeur vide.
    - **Abonnement**, **Groupe de ressources** et **Key Vault** : Entrez les valeurs correspondant au coffre de clés que vous avez créé à la section précédente.
    - **Secret** : Sélectionnez le secret nommé **Message** que vous avez créé dans la section précédente.

![Capture d’écran du formulaire Créer un nouveau Key Vault de référence](./media/create-key-vault-reference.png)

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Mettre à jour votre code pour utiliser une référence Key Vault

1. Ajoutez une référence aux packages NuGet requis en exécutant la commande suivante :

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Ouvrez *Program.cs* et ajoutez des références aux packages requis suivants :

    ```csharp
    using Azure.Identity;
    ```

1. Mettez à jour la méthode `CreateWebHostBuilder` pour utiliser App Configuration en appelant la méthode `config.AddAzureAppConfiguration`. Incluez l’option `ConfigureKeyVault` et transmettez les informations d’identification correctes à votre coffre de clés.

     #### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

1. Quand vous avez initialisé la connexion à App Configuration, vous avez configuré la connexion à Key Vault en appelant la méthode `ConfigureKeyVault`. Après l’initialisation, vous pouvez accéder aux valeurs des références Key Vault de la même façon que vous accédez aux valeurs des clés App Configuration normales.

    Pour voir ce processus en action, ouvrez *Index.cshtml* dans **Affichages** > **Dossier de base**. Remplacez son contenu par le code ci-dessous :

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Vous accédez à la valeur de la référence Key Vault **TestApp:Settings:KeyVaultMessage** de la même façon que vous accédez à la valeur de configuration **TestApp:Settings:Message**.


## <a name="grant-your-app-access-to-key-vault"></a>Autoriser votre application à accéder à Key Vault

La configuration de Azure App n’accède pas à votre coffre de clés. Votre application lira directement à partir de Key Vault. vous devez donc accorder à votre application un accès en lecture aux secrets de votre coffre de clés. De cette façon, le secret reste toujours avec votre application. L’accès peut être accordé à l’aide d’une [stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md) ou d’un [contrôle d’accès en fonction du rôle Azure](../key-vault/general/rbac-guide.md).

Vous utilisez `DefaultAzureCredential` dans votre code ci-dessus. Il s’agit d’une information d’identification de jeton agrégé qui tente automatiquement un certain nombre de types d’informations d’identification, comme `EnvironmentCredential`, `ManagedIdentityCredential`, `SharedTokenCacheCredential` et `VisualStudioCredential`. Pour plus d’informations, consultez [classe DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet). Vous pouvez remplacer `DefaultAzureCredential` explicitement par tout type d’informations d’identification. Toutefois, l’utilisation de `DefaultAzureCredential` vous permet d’avoir le même code qui s’exécute dans les environnements locaux et Azure. Par exemple, vous accordez votre propre accès aux informations d’identification à votre coffre de clés. `DefaultAzureCredential`revient automatiquement à `SharedTokenCacheCredential` ou `VisualStudioCredential` lorsque vous utilisez Visual Studio pour le développement local.

Vous pouvez également définir les variables d’environnement AZURE_TENANT_ID, AZURE_CLIENT_ID et AZURE_CLIENT_SECRET, et `DefaultAzureCredential` utilisera la clé secrète client que vous avez via l'`EnvironmentCredential` pour vous authentifier auprès de votre coffre de clés. Une fois votre application déployée sur un service Azure avec une identité gérée activée, par exemple Azure App Service, Azure Kubernetes service ou Azure Container instance, vous accordez à l’identité gérée de l’autorisation de service Azure l’autorisation d’accéder à votre coffre de clés. `DefaultAzureCredential` utilise automatiquement `ManagedIdentityCredential` lorsque votre application s’exécute dans Azure. Vous pouvez utiliser la même identité gérée pour vous authentifier avec la configuration de l’application et Key Vault. Pour plus d’informations, consultez [comment utiliser des identités gérées pour accéder à la configuration des applications](howto-integrate-azure-managed-service-identity.md).

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

    ```dotnetcli
    dotnet build
    ```

1. Une fois la génération terminée, utilisez la commande suivante pour exécuter l’application web localement :

    ```dotnetcli
    dotnet run
    ```

1. Ouvrez une fenêtre de navigateur, puis accédez à `http://localhost:5000`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application locale](./media/key-vault-reference-launch-local.png)



## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous créez une clé dans App Configuration qui référence un secret stocké dans Key Vault.
Pour savoir comment recharger automatiquement les secrets et les certificats à partir de Key Vault, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Recharger automatiquement les secrets et les certificats de Key Vault](./reload-key-vault-secrets-dotnet.md)

Pour savoir comment utiliser Managed Identity pour simplifier l’accès à la configuration et Key Vault de l’application, reportez-vous au didacticiel suivant :

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
