---
title: Tutoriel concernant l’utilisation de références Key Vault avec Azure App Configuration dans une application ASP.NET Core | Microsoft Docs
description: Dans ce tutoriel, vous apprenez à utiliser les références Key Vault avec Azure App Configuration depuis une application ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035814"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Didacticiel : Utiliser des références Key Vault dans une application ASP.NET Core

Dans ce tutoriel, vous apprenez à utiliser le service Azure App Configuration avec Azure Key Vault. Il s’agit de services complémentaires, qui seront utilisés côte à côte dans la plupart des déploiements d’applications. Pour vous aider à les utiliser ensemble, App Configuration vous permet de créer des clés qui référencent des valeurs stockées dans Key Vault. Dans ce cas, App Configuration stocke l’URI dans la valeur Key Vault, plutôt que dans la valeur proprement dite. Votre application récupère la valeur de cette clé à l’aide du fournisseur client App Configuration, comme n’importe quelle autre clé stockée dans App Configuration. Le fournisseur client la reconnaît en tant que référence Key Vault, et appelle Key Vault pour récupérer cette valeur. Votre application est chargée de s’authentifier correctement auprès d’App Configuration et auprès de Key Vault. Les deux services ne communiquent pas directement.

Ce tutoriel montre comment vous pouvez implémenter des références Key Vault dans votre code. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une clé de configuration d’application qui référence une valeur stockée dans Key Vault
> * Accéder à la valeur de cette clé à partir d’une application web ASP.NET Core

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Création d'un coffre

1. Sélectionnez l’option **Créer une ressource** dans le coin supérieur gauche du portail Azure.

    ![Sortie après la création du coffre de clés](./media/quickstarts/search-services.png)
2. Dans la zone de recherche, entrez **Key Vault**.
3. Dans la liste des résultats, choisissez **Key Vault**.
4. Dans la section Key Vault, choisissez **Créer**.
5. Dans la section **Créer un coffre de clés**, renseignez les informations suivantes :
    - **Nom** : un nom unique est obligatoire. Pour ce démarrage rapide, nous utilisons **Contoso-vault2**. 
    - **Abonnement**: Choisissez un abonnement.
    - Sous **Groupe de ressources**, choisissez **Créer** et entrez le nom du groupe de ressources.
    - Dans le menu déroulant **Emplacement**, choisissez un emplacement.
    - Conservez les valeurs par défaut des autres options.
6. Après avoir renseigné les informations ci-dessus, sélectionnez **Créer**.

À ce stade, votre compte Azure est le seul autorisé à accéder à ce nouveau coffre.

![Sortie après la création du coffre de clés](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret dans Key Vault

Pour ajouter un secret au coffre, vous devez effectuer deux autres opérations. Dans ce cas, nous ajoutons un message que nous pouvons utiliser pour tester la récupération de Key Vault. Le message est appelé **Message** et nous stockons la valeur de **Hello from Key Vault** dedans.

1. Dans les pages des propriétés Key Vault, sélectionnez **Secrets**.
1. Cliquez sur **Generate/Import (Générer/Importer)** .
1. Dans l’écran **Create a secret (Créer un secret)** , choisissez les valeurs suivantes :
    - **Options de chargement** : Manuel.
    - **Nom** : Message
    - **Valeur** : Hello from Key Vault
    - Conservez les valeurs par défaut des autres options. Cliquez sur **Créer**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Ajouter une référence Key Vault à la configuration de l’application

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance de magasin de configuration d’application que vous avez créée dans le démarrage rapide.

1. Cliquez sur **Explorateur de configuration**.

1. Cliquez sur **+ Créer** > **Référence Key Vault** et choisissez les valeurs suivantes :
    - **Clé** : TestApp:Settings:KeyVaultMessage
    - **Étiquette** : Laisser vide
    - **Abonnement**, **Groupe de ressources**, **Key Vault** : Choisissez les options correspondant au coffre Key Vault que vous avez créé à la section précédente.
    - **Secret** : Sélectionnez le secret appelé **Message** que vous avez créé dans la section précédente.

## <a name="connect-to-key-vault"></a>Se connecter à Key Vault

1. Pour ce tutoriel, vous allez utiliser un principal de service pour l’authentification auprès de KeyVault. Pour créer ce principal de service, utilisez la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Cette opération retourne une série de paires clé/valeur. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Exécutez la commande suivante pour autoriser le principal du service à accéder à votre coffre de clés :

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Ajoutez des secrets au Gestionnaire des secrets pour *clientId* et *clientSecret*. Ces commandes doivent être exécutées dans le même répertoire que le fichier *.csproj*.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Ces informations d’identification Key Vault sont utilisées uniquement dans votre application. Votre application s’authentifie directement auprès de Key Vault à l’aide de ces informations d’identification. Elles ne sont jamais transmises au service App Configuration.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Mettre à jour votre code pour utiliser une référence Key Vault

1. Ouvrez *Program.cs* et ajoutez des références aux packages voulus.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Mettez à jour la méthode `CreateWebHostBuilder` pour utiliser App Configuration en appelant la méthode `config.AddAzureAppConfiguration()`. Incluez l’option `UseAzureKeyVault` en transmettant une nouvelle référence `KeyVaultClient` à votre coffre Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Une fois que vous avez passé la référence *KeyVaultClient* à la méthode `UseAzureKeyVault` lors de l’initialisation de la connexion à App Config, vous pouvez accéder aux valeurs des références Key Vault de la même façon que vous accédez aux valeurs de clés App Config normales. Pour voir ce processus en action, ouvrez *Index.cshtml*  dans Affichages > Répertoire de base. Remplacez son contenu par le code suivant :

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Vous accédez à la valeur de la référence Key Vault *TestApp:Settings:KeyVaultMessage* de la même façon que vous accédez à la valeur de configuration *TestApp:Settings:Message*

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

        dotnet build

2. Une fois la génération correctement terminée, exécutez la commande suivante pour exécuter l’application web localement :

        dotnet run

3. Ouvrez une fenêtre de navigateur, puis accédez à `http://localhost:5000`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une identité de service managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Exemples d’interface de ligne de commande](./cli-samples.md)
