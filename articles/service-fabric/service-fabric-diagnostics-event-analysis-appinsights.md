---
title: Analyse d’événements Azure Service Fabric avec Azure Application Insights | Microsoft Docs
description: Découvrez l’analyse et la visualisation d’événements à l’aide d’Application Insights pour la surveillance et le diagnostic de clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: srrengar
ms.openlocfilehash: 29adf362fdacdb793af071fa6d7bd59214536374
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207751"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analyse et visualisation d’événements avec Application Insights

Azure Application Insights est une plateforme extensible pour la surveillance et le diagnostic d’application. Elle inclut un puissant outil d’analyse et de requête, un tableau de bord et des visualisations personnalisables, ainsi que d’autres options incluant l’alerte automatisée. Il s’agit de la plateforme recommandée pour la surveillance et le diagnostic des services et applications Service Fabric. Cet article répond aux questions les plus fréquentes ci-après :

* Comment savoir ce qui se passe à l’intérieur de mon application et de mes services et comment recueillir des données de télémétrie
* Comment résoudre les problèmes liés à mon application, et notamment aux services qui communiquent les uns avec les autres
* Comment obtenir des métriques sur les performances de mes services, comme le temps de chargement des pages ou les requêtes HTTP

Le rôle de cet article est de vous indiquer comment obtenir des informations détaillées et résoudre les problèmes à partir d’Application Insights (AI). Si vous souhaitez savoir comment installer et configurer AI avec Service Fabric, consultez ce [didacticiel](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>Surveillance dans Application Insights

Application Insights bénéficie d’une expérience riche et prête à l’emploi pour une utilisation avec Service Fabric. Dans la page de vue d’ensemble, AI fournit des informations clés concernant votre service, telles que le temps de réponse et le nombre de requêtes traitées. Vous pouvez obtenir la liste des requêtes récentes dans votre application en cliquant sur le bouton « Rechercher » dans la partie supérieure de l’écran. En outre, la page qui s’affiche vous permet de visualiser les requêtes ayant échoué et de diagnostiquer les erreurs éventuelles.

![Vue d’ensemble d’AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Dans le panneau droit de la page illustrée ci-dessus, la liste comporte deux principaux types d’entrées : les requêtes et les événements. Les requêtes sont des appels adressés à l’API de l’application par le biais de requêtes HTTP dans ce cas précis, tandis que les événements sont des événements personnalisés, qui font office de données de télémétrie que vous pouvez ajouter à un endroit quelconque de votre code. Vous pouvez obtenir plus d’informations sur l’instrumentation de vos applications en consultant l’article [API Application Insights pour les événements et les mesures personnalisés](../application-insights/app-insights-api-custom-events-metrics.md). Le fait de cliquer sur une requête vous permet de visualiser de plus amples détails comme illustré ci-après, notamment les données propres à Service Fabric, qui sont collectées dans le package NuGet AI Service Fabric. Ces informations peuvent vous aider à résoudre les problèmes et à connaître l’état de votre application, et peuvent toutes faire l’objet de recherches dans Application Insights.

![Détails d’une requête AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Le service Application Insights intègre une vue désignée pour l’interrogation de toutes les données qu’il collecte. Cliquez sur « Metrics Explorer » en haut de la page Vue d’ensemble pour accéder au portail AI. Ce portail vous permet d’exécuter des requêtes portant sur les événements personnalisés mentionnés précédemment, les exceptions, les compteurs de performances et d’autres métriques à l’aide du langage de requête Kusto. L’exemple ci-après présente toutes les requêtes exécutées au cours de la dernière heure.

![Détails d’une requête AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Pour découvrir en détail les fonctionnalités du portail Application Insights, reportez-vous à la [documentation du portail Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>Configuration de AI avec WAD

>[!NOTE]
>Cette configuration s’applique uniquement aux clusters Windows pour le moment.

Il existe deux méthodes principales permettant d’envoyer des données à Azure AI à partir de WAD, ce qui nécessite d’ajouter un récepteur AI à la configuration de WAD, comme détaillé dans [cet article](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Ajouter une clé d’instrumentation AI lors de la création d’un cluster dans le portail Azure

![Ajout d’une clé AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Lors de la création d’un cluster, si le diagnostic est « Activé » », un champ facultatif permettant d’entrer une clé d’instrumentation Application Insights s’affiche. Si vous collez votre clé AI à cet emplacement, le récepteur AI est automatiquement configuré à votre intention dans le modèle Resource Manager utilisé pour le déploiement de votre cluster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Ajouter le récepteur AI au modèle Resource Manager

Dans l’élément « WadCfg » du modèle Resource Manager, ajoutez un récepteur (« Sink ») en incluant les deux modifications suivantes :

1. Ajoutez la configuration du récepteur directement une fois que la déclaration de la `DiagnosticMonitorConfiguration` est terminée :

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Incluez le récepteur dans la `DiagnosticMonitorConfiguration` en ajoutant la ligne suivante dans la `DiagnosticMonitorConfiguration` de `WadCfg` (juste avant de déclarer les `EtwProviders`) :

    ```json
    "sinks": "applicationInsights"
    ```

Dans les deux extraits de code précédents, le nom « applicationInsights » a été utilisé pour décrire le récepteur. Cela n’est pas obligatoire, et tant que le nom du récepteur est inclus dans l’élément « sinks », vous pouvez définir n’importe quelle chaîne comme nom.

Actuellement, les journaux du cluster s’affichent sous forme de **traces** dans la visionneuse des journaux d’AI. Étant donné que la plupart des traces provenant de la plateforme sont de type « Informations », vous pouvez également envisager de modifier la configuration du récepteur pour que seuls les journaux de type « Critique » ou « Erreur » soient envoyés. Pour ce faire, ajoutez « Channels » à votre récepteur, comme illustré dans [cet article](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Si vous utilisez une clé AI incorrecte dans le portail ou dans votre modèle Resource Manager, vous devrez modifier manuellement la clé et mettre à jour/redéployer le cluster.

### <a name="configuring-ai-with-eventflow"></a>Configuration de AI avec EventFlow

Si vous utilisez EventFlow pour agréger des événements, veillez à importer le package NuGet `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`. Le code ci-après est requis dans la section *outputs* du fichier *eventFlowConfig.json* :

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Veillez à apporter les modifications obligatoires dans vos filtres, ainsi qu’à inclure toutes les autres entrées (avec leurs packages NuGet respectifs).

## <a name="aisdk"></a>SDK AI (Application Insights)

Il est recommandé d’utiliser EventFlow et WAD comme solutions d’agrégation, car ils permettent d’adopter une approche plus modulaire du diagnostic et de la surveillance. Par exemple, si vous voulez modifier vos sorties d’EventFlow, cette opération n’exige aucune modification de votre instrumentation proprement dite, mais implique une simple modification de votre fichier de configuration. Toutefois, si vous décidez d’investir dans Application Insights et qu’il est peu probable que vous changiez de plateforme, vous devez envisager d’utiliser le nouveau SDK d’AI pour agréger les événements et les envoyer à AI. Cela signifie que vous n’aurez plus à configurer EventFlow pour envoyer vos données à AI, mais simplement à installer le package NuGet Service Fabric d’Application Insights. Des informations supplémentaires sur le package sont disponibles [ici](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

La rubrique [Prise en charge d’Application Insights pour les microservices et les conteneurs](https://azure.microsoft.com/blog/app-insights-microservices/) vous présente quelques-unes des nouvelles fonctionnalités en cours de développement (encore actuellement en version bêta), qui vous permettent de bénéficier d’options de surveillance prêtes à l’emploi enrichies avec AI. Ces fonctionnalités incluent le suivi de dépendance (utilisé dans la création d’une AppMap de l’ensemble de vos services et applications dans un cluster et la communication entre eux), ainsi qu’une meilleure corrélation des suivis provenant de vos services (ce qui facilite l’identification d’un problème dans le flux de travail d’une application ou d’un service).

Si vous effectuez un développement dans .NET, que vous utiliserez probablement certains des modèles de programmation de Service Fabric et que vous souhaitez utiliser AI comme plateforme pour la visualisation et l’analyse des données d’événement et de journal, nous vous recommandons d’utiliser l’itinéraire du SDK AI comme flux de travail de surveillance et de diagnostic. Lisez [ceci](../application-insights/app-insights-asp-net-more.md) et [cela](../application-insights/app-insights-asp-net-trace-logs.md) pour commencer avec l’utilisation d’AI pour collecter et afficher vos journaux.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigation dans la ressource AI dans le portail Azure

Une fois que vous avez configuré AI comme sortie pour vos événements et journaux, les informations doivent commencer à s’afficher dans votre ressource AI au bout de quelques minutes. Accédez à la ressource AI, ce qui vous fait accéder au tableau de bord correspondant. Cliquez sur **Rechercher** dans la barre des tâches AI pour voir les dernières traces reçues et pour pouvoir les filtrer.

*Metrics Explorer* est un outil utile pour la création de tableaux de bord personnalisés basés sur les indicateurs de performance que vos applications, vos services et votre cluster peuvent signaler. Consultez [Exploration des indicateurs de performance dans Application Insights](../application-insights/app-insights-metrics-explorer.md) pour configurer vous-mêmes quelques graphiques basés sur les données que vous collectez.

Si vous cliquez sur **Analyse**, vous accédez au portail Application Insights Analytics, où vous pouvez interroger des événements et des traces avec une plus grande étendue et plus de choix. Vous pouvez en apprendre plus à ce sujet dans [Analytics dans Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des alertes dans AI](../application-insights/app-insights-alerts.md) pour être averti des changements de performances ou d’utilisation
* La [détection intelligente dans Application Insights](../application-insights/app-insights-proactive-diagnostics.md) effectue une analyse proactive de la télémétrie envoyée à AI pour vous avertir d’éventuels problèmes de performances
