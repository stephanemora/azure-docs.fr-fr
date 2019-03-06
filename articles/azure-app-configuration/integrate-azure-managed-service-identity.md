---
title: Tutoriel sur l’intégration avec des identités managées Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à utiliser des identités managées Azure pour vous authentifier auprès d’Azure App Configuration et y accéder
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957349"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Tutoriel : Intégrer avec des identités managées Azure

Les [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure Active Directory vous aident à simplifier la gestion des secrets pour votre application cloud. Avec une identité managée, vous pouvez configurer votre code pour utiliser le principal de service qui a été créé pour le service de calcul Azure sur lequel il s’exécute, au lieu d’une information d’identification distincte stockée dans Azure Key Vault ou une chaîne de connexion locale. Azure App Configuration et ses bibliothèques clientes .NET Core, .NET et Java Spring sont fournis avec une prise en charge de MSI qui leur est intégrée. Bien que vous ne soyez pas obligé de l’utiliser, MSI élimine la nécessité d’un jeton d’accès contenant des secrets. Votre code doit uniquement connaître le point de terminaison du service pour un magasin de configuration d’application afin d’y accéder, et vous pouvez incorporer cette URL directement dans votre code sans avoir à vous préoccuper de l’exposition des secrets.

Ce tutoriel montre comment vous pouvez tirer parti de MSI pour accéder à App Configuration. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Suivez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md) avant de continuer.

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. Toutefois, [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accorder à une identité managée l’accès à App Configuration
> * Configurer votre application pour utiliser une identité managée lors de la connexion à App Configuration

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* [Kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell configuré](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Ajouter une identité managée

Pour configurer une identité managée dans le portail, vous créez une application selon la procédure habituelle, puis vous activez la fonctionnalité.

1. Créez une application dans le [portail Azure](https://aka.ms/azconfig/portal) comme vous le feriez normalement. Accédez-y dans le portail.

2. Faites défiler l’écran jusqu’au groupe **Paramètres** dans le volet de navigation de gauche, puis sélectionnez **Identité**.

3. Sous l’onglet **Affecté(e) par le système**, définissez **État** sur **Activé**, puis cliquez sur **Enregistrer**.

    ![Définir une identité managée dans App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Accorder l’accès à App Configuration

1. Dans le [portail Azure](https://aka.ms/azconfig/portal), cliquez sur **Toutes les ressources** et sur le magasin de configuration d’application que vous avez créé dans le démarrage rapide.

2. Sélectionnez **Contrôle d’accès (IAM)**.

3. Sous l’onglet **Vérifier l’accès**, cliquez sur **Ajouter** dans l’interface utilisateur de carte **Ajouter une attribution de rôle**.

4. Définissez **Rôle** avec la valeur *Contributeur* et **Attribuer l’accès à** avec la valeur *App Service* (sous *Identité managée affectée par le système*).

5. Définissez **Abonnement** sur votre abonnement Azure, puis sélectionnez la ressource App Service pour votre application.

6. Cliquez sur **Enregistrer**.

    ![Ajouter une identité managée](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Utiliser une identité managée

1. Ouvrez *appsettings.json*, ajoutez le code suivant, puis remplacez *<service_endpoint>* (crochets compris) par l’URL de votre magasin de configuration d’application :

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Ouvrez *Program.cs*, puis mettez à jour la méthode `CreateWebHostBuilder` en remplaçant la méthode `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Déployer à partir de Git local

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, le plus simple consiste à utiliser Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Activer Git local avec Kudu

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, exécutez [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) dans Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pour créer une application Git, exécutez [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) dans Cloud Shell avec le paramètre `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

La sortie de la commande `az webapp create` doit ressembler à ceci :

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

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque vous êtes invité à saisir un mot de passe, veillez à utiliser celui que vous avez créé dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui vous permettant de vous connecter au portail Azure.

```bash
git push azure master
```

Vous pouvez voir une automation spécifique au runtime dans la sortie, comme MSBuild pour ASP.NET, `npm install` pour Node.js et `pip install` pour Python.

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à votre application web à l’aide d’un navigateur pour vérifier que le contenu est déployé.

```bash
http://<app_name>.azurewebsites.net
```

![Application exécutée dans App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Utiliser des identités managées dans d’autres langages

Les fournisseurs App Configuration pour .NET Framework et Java Spring disposent également d’une prise en charge intégrée de l’identité managée. Dans ce cas-là, vous utilisez simplement le point de terminaison de l’URL de votre magasin de configuration d’application au lieu de sa chaîne de connexion complète lors de la configuration d’un fournisseur. Par exemple, pour l’application console .NET Framework créée dans le démarrage rapide, vous spécifiez les paramètres suivants dans le fichier *App.config* :

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

Dans ce tutoriel, vous avez ajouté une identité de service managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Exemples d’interface de ligne de commande](./cli-samples.md)
