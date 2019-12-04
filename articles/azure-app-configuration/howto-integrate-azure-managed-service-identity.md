---
title: S’intégrer avec des identités managées Azure | Microsoft Docs
description: Découvrez comment utiliser des identités managées Azure pour vous authentifier auprès d’Azure App Configuration et y accéder
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b0c6e39aebe7864ab132805b78aa7be2d61c5160
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185139"
---
# <a name="integrate-with-azure-managed-identities"></a>Intégrer avec des identités managées Azure

Les [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure Active Directory permettent de simplifier la gestion des secrets pour votre application cloud. Grâce à une identité managée, vous pouvez configurer votre code pour utiliser le principal de service qui a été créé pour le service Azure sur lequel il s’exécute. Vous utilisez une identité managée à la place d’une information d’identification distincte stockée dans Azure Key Vault ou une chaîne de connexion locale. 

Azure App Configuration et ses bibliothèques clientes .NET Core, .NET Framework et Java Spring sont fournis avec la prise en charge de l’identité managée qui leur est intégrée. Bien que vous ne soyez pas obligé de l’utiliser, l’identité managée élimine le besoin d’un jeton d’accès contenant des secrets. Votre code peut accéder au magasin App Configuration à l’aide du point de terminaison de service uniquement. Vous pouvez incorporer cette URL directement dans votre code sans avoir à vous préoccuper de l’exposition des secrets.

Ce tutoriel montre comment vous pouvez tirer parti de l’identité managée pour accéder à App Configuration. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

De plus, ce tutoriel vous montre comment utiliser l’identité managée conjointement avec les références Key Vault d’App Configuration. Vous pouvez ainsi accéder en continu aux secrets stockés dans Key Vault, ainsi qu’aux valeurs de configuration incluses dans App Configuration. Si vous voulez explorer cette fonctionnalité, commencez par terminer [Utiliser des références Key Vault avec ASP.NET Core](./use-key-vault-references-dotnet-core.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accorder à une identité managée l’accès à App Configuration.
> * Configurer votre application pour utiliser une identité managée lors de la connexion à App Configuration.
> * Configurez éventuellement votre application pour utiliser une identité gérée quand vous vous connectez à Key Vault par le biais d’une référence Key Vault d’App Configuration.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin de :

* [SDK .NET Core](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell configuré](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Ajouter une identité managée

Pour configurer une identité managée dans le portail, vous créez d’abord une application selon la procédure habituelle, puis vous activez la fonctionnalité.

1. Créez une instance App Services dans le [portail Azure](https://portal.azure.com) comme vous le faites normalement. Accédez-y dans le portail.

2. Faites défiler l’écran jusqu’au groupe **Paramètres** dans le volet de gauche, puis sélectionnez **Identité**.

3. Sous l’onglet **Affecté(e) par le système**, définissez **État** sur **Activé**, puis sélectionnez **Enregistrer**.

4. Répondez **Oui** lorsque vous êtes invité à activer l’identité managée affectée par le système.

    ![Définir une identité managée dans App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Accorder l’accès à App Configuration

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis sélectionnez le magasin App Configuration que vous avez créé dans le guide de démarrage rapide.

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sous l’onglet **Vérifier l’accès**, sélectionnez **Ajouter** dans l’interface utilisateur de carte **Ajouter une attribution de rôle**.

1. Sous **Rôle**, sélectionnez **Contributeur**. Sous **Attribuer l’accès à**, sélectionnez **App Service** sous **Identité managée affectée par le système**.

1. Sous **Abonnement**, sélectionnez votre abonnement Azure. Sélectionnez la ressource App Service de votre application.

1. Sélectionnez **Enregistrer**.

    ![Ajouter une identité managée](./media/add-managed-identity.png)

1. Facultatif : Si vous souhaitez accorder l’accès à Key Vault également, suivez les instructions données dans [Fournir une authentification Key Vault avec une identité managée](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Utiliser une identité managée

1. Recherchez l’URL de votre magasin App Configuration en accédant à son écran de configuration dans le Portail Azure, puis en cliquant sur l’onglet **Clés d’accès**.

1. Ouvrez *appsettings.json*, puis ajoutez le script suivant. Remplacez *\<service_endpoint>* , crochets compris, par l’URL de votre magasin App Configuration. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Si vous souhaitez accéder uniquement aux valeurs stockées directement dans App Configuration, ouvrez *Program.cs* et mettez à jour la méthode `CreateWebHostBuilder` en remplaçant la méthode `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Si vous souhaitez utiliser des valeurs App Configuration et des références Key Vault, ouvrez *Program.cs* et mettez à jour la méthode `CreateWebHostBuilder` comme indiqué ci-dessous. Un `KeyVaultClient` est alors créé à l’aide d’un `AzureServiceTokenProvider` et cette référence est passée à un appel à la méthode `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Vous pouvez maintenant accéder aux références Key Vault comme n’importe quelle autre clé App Configuration. Le fournisseur de configuration utilise le `KeyVaultClient` que vous avez configuré pour l’authentification auprès de Key Vault et la récupération de la valeur.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Déployer à partir de Git local

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, le plus simple consiste à utiliser Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Activer Git local avec Kudu
Si vous n’avez pas de dépôt Git local pour votre application, vous devez en initialiser un. Pour cela, exécutez les commandes suivantes à partir du répertoire de projet de votre application :

```cmd
git init
git add .
git commit -m "Initial version"
```

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, exécutez [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) dans Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pour créer plutôt une application Git, exécutez [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) dans Cloud Shell avec le paramètre `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

La sortie de la commande `az webapp create` ressemble à ceci :

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Déployez votre projet

De retour dans la _fenêtre du terminal local_, ajoutez un référentiel distant Azure dans votre référentiel Git local. Remplacez _\<url>_ par l’URL du Git distant de la section [Activer Git pour votre application](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Quand vous êtes invité à entrer un mot de passe, entrez celui que vous avez créé dans la section [Configuration d’un utilisateur de déploiement](#configure-a-deployment-user). N’utilisez pas le mot de passe que vous utilisez pour vous connecter au portail Azure.

```bash
git push azure master
```

Vous pouvez voir une automation spécifique au runtime dans la sortie, comme MSBuild pour ASP.NET, `npm install` pour Node.js et `pip install` pour Python.

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à votre application web à l’aide d’un navigateur pour vérifier que le contenu est déployé.

```bash
http://<app_name>.azurewebsites.net
```

![Application en cours d’exécution dans App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Utiliser des identités managées dans d’autres langages

Les fournisseurs App Configuration pour .NET Framework et Java Spring disposent également d’une prise en charge intégrée de l’identité managée. Dans ce cas-là, utilisez le point de terminaison de l’URL de votre magasin App Configuration au lieu de sa chaîne de connexion complète quand vous configurez un fournisseur. Par exemple, pour l’application console .NET Framework créée dans le démarrage rapide, spécifiez les paramètres suivants dans le fichier *App.config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez ajouté une identité managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Exemples d’interface de ligne de commande](./cli-samples.md)
