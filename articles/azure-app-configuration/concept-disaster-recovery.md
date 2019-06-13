---
title: Résilience et reprise d’activité après sinistre Azure App Configuration | Microsoft Docs
description: Vue d’ensemble de l’implémentation de la résilience et de la reprise d’activité après sinistre avec Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394068"
---
# <a name="resiliency-and-disaster-recovery"></a>Résilience et reprise d’activité après sinistre

Actuellement, Azure App Configuration est un service régional. Chaque magasin de configuration est créé dans une région Azure spécifique. Une panne au niveau régional aura un impact sur tous les magasins dans cette région. App Configuration n’offre pas de basculement automatique vers une autre région. Cet article fournit des instructions générales sur l’utilisation de plusieurs magasins de configurations dans différentes régions Azure afin d’augmenter la résilience géographique de votre application.

## <a name="high-availability-architecture"></a>Architecture de haute disponibilité

Pour obtenir une redondance entre régions, vous devez créer plusieurs magasins de configuration App Configuration dans différentes régions. Avec cette configuration, votre application aura au moins un magasin de configuration supplémentaire vers lequel basculer si le magasin principal devient inaccessible. Voici un diagramme qui illustre la topologie entre votre application et ses magasins de configuration principal et secondaire.

![Stockages géoredondants](./media/geo-redundant-app-configuration-stores.png)

Votre application chargera en parallèle sa configuration à partir des magasins principal et secondaire. Cette opération augmente sensiblement vos chances d’obtenir les données de configuration. Vous êtes responsable de la synchronisation des données dans les deux magasins. Les sections suivantes expliquent comment configurer la résilience géographique pour votre application.

## <a name="failover-between-configuration-stores"></a>Basculement entre les magasins de configuration

Techniquement, votre application n’effectue aucun un basculement. Elle tente d’extraire simultanément le même jeu de données de configuration à partir de deux magasins de configuration d’applications. Vous devez modifier votre code afin qu’il charge d’abord les données à partir du magasin secondaire puis du magasin principal. Cette approche garantit la priorité des données de configuration lorsqu’elles sont disponibles dans le magasin principal. L’extrait de code ci-dessous montre comment implémenter cette approche dans .NET Core.

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
    }
```

Notez que le paramètre `optional` est passé dans la fonction `AddAzureAppConfiguration`. Lorsqu’il est défini sur `true`, ce paramètre empêchera l’arrêt de l’application si la fonction est incapable de charger les données de configuration.

## <a name="synchronization-between-configuration-stores"></a>Synchronisation entre les magasins de configuration

Il est important que vos magasins de configuration géoredondants disposent tous du même jeu de données. Vous pouvez utiliser la fonction d’**exportation** d’App Configuration pour copier à la demande des données du magasin principal vers le magasin secondaire. Cette fonction est disponible via le portail Azure et l’interface CLI.

Sur le portail Azure, vous pouvez envoyer (push) une modification vers un autre magasin de configuration en procédant comme suit :

1. Accédez à l’onglet **Import/Export** (Importer/Exporter), sélectionnez **Export** (Exporter), **App Configuration** comme service **Target** (cible), puis cliquez sur **Select a resource** (Sélectionner une ressource).

2. Dans le nouveau panneau qui s’ouvre, spécifiez l’abonnement, le groupe de ressources et le nom de la ressource de votre magasin secondaire, puis cliquez sur **Apply** (Appliquer).

3. L’interface utilisateur sera mise à jour pour vous permettre de choisir les données de configuration à exporter vers votre magasin secondaire. Vous pouvez conserver la valeur de temps par défaut et définir les deux options **From label** (De l’étiquette) et **To Label** (À l’étiquette) sur la même valeur. Cliquez sur **Appliquer**.

4. Répétez les étapes ci-dessus pour toutes les modifications de configuration.

Vous pouvez automatiser ce processus d’exportation à l’aide de l’interface CLI. La commande ci-dessous montre comment exporter une modification de configuration du magasin principal vers le magasin secondaire.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à optimiser votre application pour obtenir une résilience géographique pendant l’exécution pour App Configuration. Vous pouvez également incorporer des données de configuration à partir d’App Configuration au moment de la génération ou du déploiement. Pour plus d’informations, consultez [Intégration à un pipeline CI/CD](./integrate-ci-cd-pipeline.md).

