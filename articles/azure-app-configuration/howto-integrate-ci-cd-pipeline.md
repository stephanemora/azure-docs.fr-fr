---
title: Intégrer avec un pipeline d’intégration et la livraison continu à l’aide de Configuration de l’application Azure | Microsoft Docs
description: Découvrez comment générer un fichier de configuration en utilisant les données de Configuration de l’application Azure pendant l’intégration et livraison continues
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224326"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Intégrer avec un pipeline CI/CD

Vous pouvez améliorer la résilience de votre application contre la possibilité à distance n’est pas en mesure d’atteindre la Configuration de l’application Azure. Pour ce faire, empaqueter les données de configuration en cours dans un fichier qui est déployé avec l’application et chargés localement pendant son démarrage. Cette approche garantit que votre application possède au moins les valeurs de paramètre par défaut. Ces valeurs sont remplacées par les modifications plus récentes dans un magasin de configuration d’application lorsqu’elle est disponible.

À l’aide de la [exporter](./howto-import-export-data.md#export-data) fonction de Configuration de l’application Azure, vous pouvez automatiser le processus de récupération des données de configuration actuel en un seul fichier. Ensuite incorporer ce fichier dans une étape de génération ou de déploiement de votre pipeline d’intégration et déploiement continus (CI/CD).

L’exemple suivant montre comment inclure la Configuration de l’application pour l’application web introduite dans les Démarrages rapides des données en tant qu’une build étape. Avant de continuer, Terminer [créer une application ASP.NET Core avec Configuration d’application](./quickstart-aspnet-core-app.md) première.

Vous pouvez utiliser n’importe quel éditeur de code pour effectuer les étapes dans ce démarrage rapide. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur le Windows, macOS et les plateformes Linux.

## <a name="prerequisites"></a>Conditions préalables

Si vous créez localement, téléchargez et installez le [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) si vous n’avez pas déjà.

Pour faire une build de cloud, avec Azure DevOps, par exemple, assurez-vous que le [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) est installé dans votre système de génération.

## <a name="export-an-app-configuration-store"></a>Exporter un magasin de configuration d’applications

1. Ouvrez votre *.csproj* fichier, puis ajoutez le script suivant :

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Ajouter le *ConnectionString* associé à votre magasin de configuration d’application comme une variable d’environnement.

2. Ouvrez le fichier Program.cs et mettre à jour le `CreateWebHostBuilder` méthode à utiliser le fichier JSON exporté en appelant le `config.AddJsonFile()` (méthode).

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

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString**et affectez-lui la valeur de la clé d’accès à votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite de commandes pour permettre la modification prenne effet :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Si vous utilisez Mac OS ou Linux, exécutez la commande suivante :

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interface de commande :

        dotnet build

3. Une fois la génération terminée avec succès, exécutez la commande suivante pour exécuter l’application web localement :

        dotnet run

4. Ouvrez une fenêtre de navigateur et accédez à `http://localhost:5000`, qui est l’URL par défaut pour l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Étapes suivantes

* [Les identités pour l’intégration de ressources Azure](./integrate-azure-managed-service-identity.md)
