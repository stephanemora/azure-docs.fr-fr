---
title: 'Tutoriel : Utiliser la configuration dynamique avec l’actualisation en mode push dans une application .NET Core'
titleSuffix: Azure App Configuration
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour des applications .NET Core avec l’actualisation en mode push.
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 977982bf1a36b4b85524df2513f2272fe4a8d1bf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701516"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Tutoriel : Utiliser la configuration dynamique avec l’actualisation en mode push dans une application .NET Core

La bibliothèque de client .NET Core App Configuration prend en charge la mise à jour de la configuration à la demande, sans entraîner le redémarrage de l’application. Une application peut être configurée pour détecter les changements dans App Configuration à l’aide de l’une ou des deux approches suivantes.

1. Modèle par interrogation (polling) : il s’agit du comportement par défaut qui utilise l’interrogation pour détecter des changements de configuration. Quand la valeur mise en cache d’un paramètre expire, l’appel suivant à `TryRefreshAsync` ou à `RefreshAsync` envoie une demande au serveur pour vérifier si la configuration a changé et tire (pull) la configuration mise à jour si nécessaire.

1. Modèle d’envoi (push) : utilise les [événements App Configuration](./concept-app-configuration-event.md) pour détecter des changements de configuration. Une fois App Configuration configuré pour envoyer les événements de changement de paire clé-valeur à Azure Event Grid, l’application peut utiliser ces événements pour optimiser le nombre total de demandes nécessaires pour tenir la configuration à jour. Les applications peuvent s’abonner à ces événements directement à partir d’Event Grid ou par le biais de l’un des [gestionnaires d’événements pris en charge](../event-grid/event-handlers.md) (comme un webhook, une fonction Azure ou une rubrique Service Bus).

Les applications peuvent s’abonner à ces événements directement à partir d’Event Grid ou par le biais d’un webhook, ou encore en transférant les événements à Azure Service Bus. Le SDK Azure Service Bus fournit une API d’inscription de gestionnaire de messages qui simplifie ce processus pour les applications qui n’ont pas de point de terminaison HTTP ou qui ne souhaitent pas interroger constamment la grille d’événements sur les changements.

Ce tutoriel vous montre comment implémenter des mises à jour de la configuration dynamique dans votre code à l’aide de l’actualisation en mode push. Il s’appuie sur l’application mentionnée dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application .NET Core avec App Configuration](./quickstart-dotnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un abonnement pour envoyer des événements de changement de configuration d’App Configuration à une rubrique Service Bus
> * Configurer votre application .NET Core pour mettre à jour sa configuration en réponse aux changements survenant dans App Configuration
> * Utiliser la configuration la plus récente dans votre application.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Configurer une rubrique et un abonnement Azure Service Bus

Ce tutoriel utilise l’intégration à Service Bus pour Event Grid afin de simplifier la détection des changements de configuration pour les applications qui ne souhaitent pas interroger constamment App Configuration sur les changements. Le SDK Azure Service Bus fournit une API d’inscription de gestionnaire de messages qui peut être utilisée pour mettre à jour la configuration quand des changements sont détectés dans App Configuration. Suivez les étapes dans [Démarrage rapide : Utiliser le portail Azure pour créer une rubrique Service Bus et un abonnement](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) pour créer un espace de noms, une rubrique et un abonnement Service Bus.

Une fois les ressources créées, ajoutez les variables d’environnement suivantes. Celles-ci permettent d’inscrire un gestionnaire d’événements utilisé pour les changements de configuration dans le code de l’application.

| Clé | Valeur |
|---|---|
| ServiceBusConnectionString | Chaîne de connexion pour l’espace de noms Service Bus |
| ServiceBusTopic | Nom de la rubrique Service Bus |
| ServiceBusSubscription | Nom de l’abonnement Service Bus |

## <a name="set-up-event-subscription"></a>Configurer l’abonnement aux événements

1. Ouvrez la ressource App Configuration dans le portail Azure, puis cliquez sur `+ Event Subscription` dans le volet `Events`.

    ![Événements App Configuration](./media/events-pane.png)

1. Entrez un nom pour `Event Subscription` et `System Topic`.

    ![Créer un abonnement aux événements](./media/create-event-subscription.png)

1. Sélectionnez `Service Bus Topic` comme `Endpoint Type`, choisissez la rubrique Service Bus, puis cliquez sur `Confirm Selection`.

    ![Point de terminaison Service Bus de l’abonnement aux événements](./media/event-subscription-servicebus-endpoint.png)

1. Cliquez sur `Create` pour créer l’abonnement aux événements.

1. Cliquez sur `Event Subscriptions` dans le volet `Events` pour valider que l’abonnement a bien été créé.

    ![Abonnements aux événements App Configuration](./media/event-subscription-view.png)

> [!NOTE]
> Quand vous vous abonnez à des changements de configuration, vous pouvez utiliser un ou plusieurs filtres pour réduire le nombre d’événements envoyés à votre application. Vous pouvez les configurer comme [filtres d’abonnement Event Grid](../event-grid/event-filtering.md) ou comme [filtres d’abonnement Service Bus](../service-bus-messaging/topic-filters.md). Par exemple, vous pouvez utiliser un filtre d’abonnement pour vous abonner uniquement aux événements liés à des changements dans une clé qui commence par une chaîne spécifique.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Inscrire un gestionnaire d’événements pour recharger des données à partir d’App Configuration

Ouvrez *Program.cs* et mettez à jour le fichier avec le code suivant.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

La méthode [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) permet de définir la valeur mise en cache des paires clé-valeur inscrites à l’actualisation comme modifiées (« dirty »). De cette façon, le prochain appel à `RefreshAsync` ou à `TryRefreshAsync` revalide les valeurs mises en cache avec la configuration de l’application et les met à jour si nécessaire.

Un délai aléatoire est ajouté avant le marquage de la valeur mise en cache comme modifiée pour réduire la limitation potentielle en cas d’actualisation de plusieurs instances en même temps. Le délai maximal par défaut avant le marquage de la valeur mise en cache comme modifiée est de 30 secondes, mais vous pouvez le changer en passant un paramètre `TimeSpan` facultatif à la méthode `SetDirty`.

> [!NOTE]
> Pour réduire le nombre de demandes adressées à App Configuration lors de l’utilisation de l’actualisation en mode push, il est important d’appeler `SetCacheExpiration(TimeSpan cacheExpiration)` avec une valeur appropriée du paramètre `cacheExpiration`. Cela permet de contrôler le délai d’expiration du cache pour l’actualisation en mode push et d’avoir un filet de sécurité en cas de problème avec l’abonnement aux événements ou l’abonnement Service Bus. `TimeSpan.FromDays(30)` est la valeur recommandée.

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **AppConfigurationConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin App Configuration. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Exécutez la commande suivante pour générer l’application console :

    ```console
     dotnet build
    ```

1. La génération terminée correctement, lancez la commande suivante pour exécuter l’application localement :

    ```console
     dotnet run
    ```

    ![Exécution de l’actualisation en mode push avant la mise à jour](./media/dotnet-core-app-pushrefresh-initial.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

1. Sélectionnez **Explorateur de configuration**, puis mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Données issues d’Azure App Configuration - Mise à jour |

1. Attendez 30 secondes pour permettre le traitement de l’événement et la mise à jour de la configuration.

    ![Exécution de l’actualisation en mode push après la mise à jour](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application .NET Core d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)