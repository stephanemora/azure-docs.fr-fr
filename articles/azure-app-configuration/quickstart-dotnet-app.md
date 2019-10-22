---
title: Démarrage rapide pour Azure App Configuration avec .NET Framework | Microsoft Docs
description: Guide de démarrage rapide pour utiliser Azure App Configuration avec des applications .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/09/2019
ms.author: lcozzens
ms.openlocfilehash: 17b2e7272d499ce99d40d2ee52de1c7a5a1d0d04
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329793"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application .NET Framework avec Azure App Configuration

Dans ce guide de démarrage rapide, vous intégrez Azure App Configuration dans une application console .NET Framework pour centraliser le stockage et la gestion des paramètres d’application en dehors de votre code.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Créer un magasin de configuration d’application

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Explorateur de configurations** >  **+ Créer** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

## <a name="create-a-net-console-app"></a>Créer une application console .NET

1. Démarrez Visual Studio, puis sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Create un nouveau projet**, filtrez sur le type de projet **Console** et cliquez sur **Application console (.NET Framework)** . Cliquez sur **Suivant**.

1. Dans **Configurer votre nouveau projet** , entrez un nom de projet. Sous **Framework**, sélectionnez **.NET Framework 4.7.1** ou version ultérieure. Cliquez sur **Créer**.

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin de configuration d’application

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

   La chaîne de connexion de votre magasin de configuration d’application est lue à partir de la variable d’environnement `ConnectionString`. Ajoutez le générateur de configuration `Environment` avant `MyConfigStore` dans la propriété `configBuilders` à la section `appSettings`.

1. Ouvrez *Program.cs*, puis mettez à jour la méthode `Main` pour utiliser App Configuration en appelant `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la chaîne de connexion de votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Redémarrez Visual Studio pour que la modification soit prise en compte. Appuyez sur Ctrl + F5 pour générer et exécuter l’application console.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin de configuration d’application et vous l’avez utilisé avec une application console .NET Framework. Pour en savoir plus sur la façon d’utiliser App Configuration, passez au tutoriel suivant et découvrez l’authentification.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
