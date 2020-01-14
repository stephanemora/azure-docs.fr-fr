---
title: 'Tutoriel : Intégrer avec un pipeline d’intégration et livraison continus'
titleSuffix: Azure App Configuration
description: Dans ce tutoriel, vous allez apprendre à créer un fichier de configuration en utilisant des données dans Azure App Configuration pendant l’intégration et la livraison continues
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: f8adfee1e707181e78f4e845f61477b08af935e9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433638"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Intégrer avec un pipeline CI/CD

Cet article décrit différentes façons d’utiliser les données d’Azure App Configuration dans un système d’intégration continue et de déploiement continu.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Utiliser App Configuration dans votre pipeline Azure DevOps

Si vous utilisez un pipeline Azure DevOps, vous pouvez extraire des paires clé-valeur d’App Configuration et les définir en tant que variables de tâche. L’[extension DevOps Azure App Configuration](https://go.microsoft.com/fwlink/?linkid=2091063) est un module complémentaire qui fournit cette fonctionnalité. Suivez simplement ses instructions pour utiliser l’extension dans une séquence de tâches de build ou de mise en production.

## <a name="deploy-app-configuration-data-with-your-application"></a>Déployer des données App Configuration avec votre application

Votre application risque de ne pas s’exécuter si elle dépend d’Azure App Configuration et ne peut pas y accéder. Vous pouvez améliorer la résilience de votre application pour gérer un tel événement, même s’il est peu probable qu’il se produise. Pour ce faire, créez un package des données de configuration actuelles dans un fichier qui sera déployé avec l’application et chargé localement pendant son démarrage. Cette approche garantit que votre application contienne au moins les valeurs de paramètre par défaut. Ces valeurs sont remplacées par les modifications plus récentes dans un magasin App Configuration lorsqu’elles sont disponibles.

À l’aide de la fonction [Exporter](./howto-import-export-data.md#export-data) fonction d’Azure App Configuration, vous pouvez automatiser le processus de récupération des données de configuration actuelles dans un seul fichier. Ensuite, pendant une étape de création ou de déploiement, incorporez ce fichier dans votre pipeline d’intégration et déploiement continus (CI/CD).

L’exemple suivant montre comment inclure les données d’App Configuration en tant qu’étape de création pour l’application web introduite dans les démarrages rapides. Avant de continuer, terminez d’abord l’étape [Créer une application ASP.NET Core avec App Configuration](./quickstart-aspnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

### <a name="prerequisites"></a>Conditions préalables requises

Si vous créez localement, téléchargez et installez l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) si vous ne l’avez pas déjà fait.

Pour faire une build de cloud, avec Azure DevOps, par exemple, assurez-vous que l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) est installée dans votre système de build.

### <a name="export-an-app-configuration-store"></a>Exporter un magasin App Configuration

1. Ouvrez votre fichier *.csproj* et ajoutez le script suivant :

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Ajoutez *ConnectionString* associé à votre magasin App Configuration comme variable d’environnement.

2. Ouvrez *Program.cs*, puis mettez à jour la méthode `CreateWebHostBuilder` pour utiliser le fichier JSON exporté en appelant la méthode `config.AddJsonFile()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin App Configuration. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

        dotnet build

3. Une fois la génération correctement terminée, exécutez la commande suivante pour exécuter l’application web localement :

        dotnet run

4. Ouvrez une fenêtre de navigateur, puis accédez à `http://localhost:5000`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez exporté des données d’Azure App Configuration à utiliser dans un pipeline de déploiement. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
