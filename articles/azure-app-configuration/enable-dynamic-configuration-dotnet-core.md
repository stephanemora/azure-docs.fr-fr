---
title: 'Tutoriel : Utiliser la configuration dynamique dans une application .NET Core'
titleSuffix: Azure App Configuration
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: jken
ms.openlocfilehash: 9a2a8f88cd39b452a54d6fc668737fc1b5be68cc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996964"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application .NET Core

La bibliothèque du fournisseur .NET d’App Configuration prend en charge la mise à jour de la configuration à la demande sans entraîner le redémarrage de l’application. Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application présentée dans le guide de démarrage rapide. Vous devez finir de [Créer une application .NET Core avec App Configuration](./quickstart-dotnet-core-app.md) avant de continuer.

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre application .NET Core pour mettre à jour sa configuration en réponse aux changements survenant dans un magasin App Configuration.
> * Utiliser la configuration la plus récente dans votre application.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Finissez le guide de démarrage rapide [Créer une application .NET Core avec App Configuration](./quickstart-dotnet-core-app.md).

## <a name="activity-driven-configuration-refresh"></a>Actualisation de la configuration basée sur l’activité

Ouvrez *Program.cs* et mettez à jour le code comme indiqué ci-dessous.

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private static IConfiguration _configuration = null;
        private static IConfigurationRefresher _refresher = null;

        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder();
            builder.AddAzureAppConfiguration(options =>
            {
                options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                        .ConfigureRefresh(refresh =>
                        {
                            refresh.Register("TestApp:Settings:Message")
                                   .SetCacheExpiration(TimeSpan.FromSeconds(10));
                        });

                _refresher = options.GetRefresher();
            });

            _configuration = builder.Build();
            PrintMessage().Wait();
        }

        private static async Task PrintMessage()
        {
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

            // Wait for the user to press Enter
            Console.ReadLine();

            await _refresher.TryRefreshAsync();
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
        }
    }
}
```

Dans la méthode `ConfigureRefresh`, une clé de votre magasin App Configuration est inscrite pour la supervision des changements. La méthode `Register` a un paramètre booléen facultatif `refreshAll` qui permet d’indiquer si toutes les valeurs de configuration doivent être actualisées en cas de changement de la clé inscrite. Dans cet exemple, seule la clé *TestApp:Settings:Message* est actualisée. La méthode `SetCacheExpiration` spécifie le temps minimum qui doit s'écouler avant qu'une nouvelle requête puisse être adressée à App Configuration pour rechercher les changements de configuration. Dans cet exemple, vous remplacez le délai d’expiration par défaut de 30 secondes par un délai de 10 secondes à des fins de démonstration.

L’appel de la méthode `ConfigureRefresh` seule n’entraîne pas l’actualisation automatique de la configuration. Vous appelez la méthode `TryRefreshAsync` à partir de l’interface `IConfigurationRefresher` pour déclencher une actualisation. Cette façon de procéder permet d’éviter l’envoi de requêtes fantômes à App Configuration même quand votre application est inactive. Vous devez inclure l’appel de `TryRefreshAsync` quand vous considérez que votre application est active. Par exemple, cela peut se produire quand vous traitez un message entrant, une commande ou l’itération d’une tâche complexe. Il peut également s’agir d’un minuteur si votre application est active en permanence. Dans cet exemple, vous appelez `TryRefreshAsync` chaque fois que vous appuyez sur la touche Entrée. Notez que, même en cas d’échec de l’appel de `TryRefreshAsync` pour une raison quelconque, votre application continue à utiliser la configuration mise en cache. Une autre tentative a lieu quand le délai d’expiration configuré pour le cache est écoulé et que l’appel de `TryRefreshAsync` est à nouveau déclenché par l’activité de votre application. L’appel de `TryRefreshAsync` est une non-opération avant l’expiration du délai configuré pour le cache. Son impact sur les performances est donc minimal, même si l’appel est effectué fréquemment.

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin App Configuration. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Exécutez la commande suivante pour générer l’application console :

    ```console
     dotnet build
    ```

1. La génération terminée correctement, lancez la commande suivante pour exécuter l’application localement :

    ```console
     dotnet run
    ```

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/dotnet-core-app-run.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

1. Sélectionnez **Explorateur de configuration**, puis mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Données issues d’Azure App Configuration - Mise à jour |

1. Appuyez sur la touche Entrée pour déclencher une actualisation et afficher la valeur mise à jour dans l’invite de commandes ou dans la fenêtre PowerShell.

    ![Démarrage rapide de l’actualisation locale de l’application](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Étant donné que le délai d’expiration du cache a été défini sur 10 secondes à l’aide de la méthode `SetCacheExpiration` lors de la spécification de la configuration de l’opération d’actualisation, la valeur du paramètre de configuration ne sera actualisée que si 10 secondes se sont écoulées depuis la dernière actualisation de ce paramètre.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application .NET Core d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
