---
title: Démarrage rapide pour Azure App Configuration avec .NET Framework | Microsoft Docs
description: Guide de démarrage rapide pour utiliser Azure App Configuration avec des applications .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 8190265753bddb3038c5403411c4be193dd8075c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433618"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application .NET Framework avec Azure App Configuration

Dans ce guide de démarrage rapide, vous intégrez Azure App Configuration dans une application console .NET Framework pour centraliser le stockage et la gestion des paramètres d’application en dehors de votre code.

## <a name="prerequisites"></a>Conditions préalables requises

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Explorateur de configurations** > **Créer** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

## <a name="create-a-net-console-app"></a>Créer une application console .NET

1. Démarrez Visual Studio, puis sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Create un nouveau projet**, filtrez sur le type de projet **Console** et cliquez sur **Application console (.NET Framework)** . Sélectionnez **Suivant**.

1. Dans **Configurer votre nouveau projet** , entrez un nom de projet. Sous **Framework**, sélectionnez **.NET Framework 4.7.1** ou version ultérieure. Sélectionnez **Create** (Créer).

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

1. Cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, recherchez et ajoutez les packages NuGet suivants à votre projet. Si vous ne les trouvez pas, cochez la case **Inclure les préversions**.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Mettez à jour le fichier *App.config* de votre projet comme suit :

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   La chaîne de connexion de votre magasin App Configuration est lue à partir de la variable d’environnement `ConnectionString`. Ajoutez le générateur de configuration `Environment` avant `MyConfigStore` dans la propriété `configBuilders` à la section `appSettings`.

1. Ouvrez *Program.cs*, puis mettez à jour la méthode `Main` pour utiliser App Configuration en appelant `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la chaîne de connexion de votre magasin App Configuration. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante :

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Redémarrez Visual Studio pour que la modification soit prise en compte. Appuyez sur Ctrl + F5 pour générer et exécuter l’application console.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin App Configuration et vous l’avez utilisé avec une application console .NET Framework. La valeur `AppSettings` de `ConfigurationManager` ne change pas après le démarrage de l’application. Toutefois, la bibliothèque du fournisseur de configuration .NET Standard App Configuration peut également être utilisée dans une application .NET Framework. Pour savoir comment permettre à votre application .NET Framework d’actualiser dynamiquement les paramètres de configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Activer la configuration dynamique](./enable-dynamic-configuration-dotnet.md)
