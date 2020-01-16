---
title: Résilience et récupération d'urgence Azure App Configuration
description: Découvrez comment implémenter la résilience et la récupération d'urgence avec Azure App Configuration.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: cd706e42eff19ebacf92b77d2438af80dc16a5fb
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028245"
---
# <a name="resiliency-and-disaster-recovery"></a>Résilience et reprise d’activité après sinistre

Actuellement, Azure App Configuration est un service régional. Chaque magasin de configuration est créé dans une région Azure spécifique. Une panne au niveau régional affecte tous les magasins de cette région. App Configuration n’offre pas de basculement automatique vers une autre région. Cet article fournit des instructions générales sur l’utilisation de plusieurs magasins de configurations dans différentes régions Azure afin d’augmenter la résilience géographique de votre application.

## <a name="high-availability-architecture"></a>Architecture de haute disponibilité

Pour obtenir une redondance entre régions, vous devez créer plusieurs magasins App Configuration dans différentes régions. Avec cette configuration, votre application a au moins un magasin de configuration supplémentaire vers lequel basculer si le magasin principal devient inaccessible. Le diagramme suivant illustre la topologie entre votre application et ses magasins de configuration principal et secondaire :

![Stockages géoredondants](./media/geo-redundant-app-configuration-stores.png)

Votre application charge en parallèle sa configuration à partir des magasins principal et secondaire. Cette opération augmente les chances d’obtenir les données de configuration. Vous êtes responsable de la synchronisation des données dans les deux magasins. Les sections suivantes expliquent comment configurer la résilience géographique pour votre application.

## <a name="failover-between-configuration-stores"></a>Basculement entre les magasins de configuration

Techniquement, votre application n’effectue pas de basculement. Elle tente d’extraire simultanément le même jeu de données de configuration à partir de deux magasins App Configuration. Organisez votre code afin qu’il charge d’abord les données à partir du magasin secondaire, puis du magasin principal. Cette approche garantit que les données de configuration du magasin principal sont prioritaires quand elles sont disponibles. L’extrait de code suivant montre comment vous pouvez implémenter cette organisation dans l’interface CLI .NET Core :

#### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Notez que le paramètre `optional` est passé dans la fonction `AddAzureAppConfiguration`. Quand il est défini sur `true`, ce paramètre empêche l’arrêt de l’application si la fonction ne peut pas charger les données de configuration.

## <a name="synchronization-between-configuration-stores"></a>Synchronisation entre les magasins de configuration

Il est important que vos magasins de configuration géoredondants disposent tous du même jeu de données. Vous pouvez utiliser la fonction **Export** d’App Configuration pour copier des données à la demande du magasin principal vers le magasin secondaire. Cette fonction est disponible via le portail Azure et l’interface CLI.

Dans le portail Azure, vous pouvez envoyer (push) une modification vers un autre magasin de configuration en suivant ces étapes.

1. Accédez à l’onglet **Importer/Exporter** et sélectionnez **Exporter** > **Configuration de l’application** > **Cible** > **Sélectionner une ressource**.

2. Dans le nouveau panneau qui s’ouvre, spécifiez l’abonnement, le groupe de ressources et le nom de la ressource de votre magasin secondaire, puis cliquez sur **Appliquer**.

3. L’interface utilisateur est mise à jour pour vous permettre de choisir les données de configuration à exporter vers votre magasin secondaire. Vous pouvez conserver la valeur de temps par défaut et définir les deux options **Étiquette d’expédition** et **Étiquette de destination** sur la même valeur. Sélectionnez **Appliquer**.

4. Répétez les étapes précédentes pour toutes les modifications de configuration.

Pour automatiser ce processus d’exportation, utilisez Azure CLI. La commande suivante montre comment exporter une modification de configuration du magasin principal vers le magasin secondaire :

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à optimiser votre application pour obtenir une résilience géographique pendant l’exécution pour App Configuration. Vous pouvez aussi incorporer des données de configuration à partir d’App Configuration au moment de la génération ou du déploiement. Pour plus d’informations, consultez [Intégration à un pipeline CI/CD](./integrate-ci-cd-pipeline.md).

