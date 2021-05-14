---
title: Démarrage rapide - Supervision des applications Azure Spring Cloud avec les journaux, les métriques et le suivi
description: Utilisez le streaming de journaux, Log Analytics, les métriques et le suivi pour superviser les exemples d’applications PetClinic sur Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 04/23/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 9275eb9d5f3206a5acd41fa970c4acd1f4663a37
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108321570"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Démarrage rapide : Supervision des applications Azure Spring Cloud avec les journaux, les métriques et le suivi

::: zone pivot="programming-language-csharp"
Avec les fonctionnalités de supervision intégrées d’Azure Spring Cloud, vous pouvez déboguer et superviser des problèmes complexes. Azure Spring Cloud intègre le [suivi distribué](https://steeltoe.io/docs/3/tracing/distributed-tracing) de Steeltoe dans [Application Insights](../azure-monitor/app/app-insights-overview.md). Cette intégration fournit des fonctionnalités puissantes en matière de journaux, de métriques et de suivi distribué, disponibles dans le portail Azure.

Les procédures suivantes expliquent comment utiliser le streaming de journaux, l’analytique des journaux, les métriques et le suivi distribué avec l’exemple d’application que vous avez déployé dans les guides de démarrage rapide précédents.

## <a name="prerequisites"></a>Prérequis

* Suivez les guides de démarrage rapide précédents de cette série :

  * [Provisionner le service Azure Spring Cloud](./quickstart-provision-service-instance.md).
  * [Configurer un serveur de configuration Azure Spring Cloud](./quickstart-setup-config-server.md).
  * [Créer et déployer des applications](./quickstart-deploy-apps.md)

## <a name="logs"></a>Journaux d’activité

Il existe deux façons de consulter les journaux sur Azure Spring Cloud : Le **streaming de journaux**  pour les journaux en temps réel par instance d’application ou **Log Analytics** pour les journaux agrégés avec fonction de requête avancée.

### <a name="log-streaming"></a>Diffusion de journaux

Vous pouvez utiliser le streaming de journaux dans Azure CLI avec la commande suivante.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Le résultat ressemble à l’exemple suivant :

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Utilisez `az spring-cloud app logs -h` pour explorer davantage de paramètres et de fonctionnalités de streaming de journaux.

### <a name="log-analytics"></a>Log Analytics

1. Dans le portail Azure, accédez à la page **Service | Vue d’ensemble**, puis sélectionnez **Journaux** dans la section **Supervision**. Sélectionnez **Exécuter** pour l’un des exemples de requête Azure Spring Cloud.

   [ ![Entrée Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Modifiez la requête pour supprimer les clauses WHERE qui limitent l’affichage aux journaux d’avertissements et d’erreurs.

1. Sélectionnez ensuite `Run` pour afficher les journaux. Pour plus d’informations sur l’écriture de requêtes, consultez [Documentation Azure Log Analytics](../azure-monitor/logs/get-started-queries.md).

   [ ![Requête Logs Analytics - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Pour en savoir plus sur le langage de requête qui est utilisé dans Log Analytics, consultez [Requêtes de journal Azure Monitor](/azure/data-explorer/kusto/query/). Pour interroger tous vos journaux Log Analytics à partir d’un client centralisé, consultez [Explorateur de données Azure](/azure/data-explorer/query-monitor-data).

## <a name="metrics"></a>Mesures

1. Dans le portail Azure, accédez à la page **Service | Vue d’ensemble**, puis sélectionnez **Métriques** dans la section **Supervision**. Ajoutez votre première métrique en sélectionnant l’une des métriques .NET sous **Performance (.NET)** ou **Requête (.NET)** dans la liste déroulante **Métrique**, puis `Avg` pour que **Agrégation** puisse voir la chronologie de cette métrique.

   [ ![Entrée de métriques - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Cliquez sur **Ajouter un filtre** dans la barre d’outils, puis sélectionnez `App=solar-system-weather` pour voir l’utilisation du processeur uniquement pour l’application **solar-system-weather**.

   [ ![Utiliser un filtre dans les métriques - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Abandonnez le filtre créé à l’étape précédente, sélectionnez **Appliquer la division**, puis sélectionnez `App` pour **Valeurs** afin de voir l’utilisation du processeur par les différentes applications.

   [ ![Appliquer la division dans les métriques - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Traçage distribué

1. Dans le portail Azure, accédez à la page **Service | Vue d’ensemble**, puis sélectionnez **Suivi distribué** dans la section **Supervision**. Ensuite, sélectionnez l’onglet **Afficher la cartographie d’application** sur la droite.

   [ ![Entrée Suivi distribué - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Vous pouvez maintenant voir l’état des appels entre les applications. 

   [ ![Vue d’ensemble du suivi distribué - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Sélectionnez le lien entre **solar-system-weather** et **planet-weather-provider** pour voir plus de détails, comme les appels les plus lents émis par les méthodes HTTP.

   [ ![Suivi distribué - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Enfin, sélectionnez **Examiner les performances** pour explorer plus en détail l’analyse des performances intégrée.

   [ ![Performances du suivi distribué - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Avec les fonctionnalités de supervision intégrées d’Azure Spring Cloud, vous pouvez déboguer et superviser des problèmes complexes. Azure Spring Cloud intègre Azure [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) au service [Application Insights](../azure-monitor/app/app-insights-overview.md) d’Azure. Cette intégration fournit des fonctionnalités puissantes en matière de journaux, de métriques et de suivi distribué, disponibles dans le portail Azure. Les procédures suivantes expliquent comment utiliser le streaming de journaux, Log Analytics, les métriques et le suivi distribué avec les applications PetClinic déployées.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes précédentes : 

* [Provisionner une instance d’Azure Spring Cloud](./quickstart-provision-service-instance.md)
* [Configurer le serveur de configuration](./quickstart-setup-config-server.md)
* [Créer et déployer des applications](./quickstart-deploy-apps.md)

## <a name="logs"></a>Journaux d’activité

Il existe deux façons de consulter les journaux sur Azure Spring Cloud : Le **streaming de journaux**  pour les journaux en temps réel par instance d’application ou **Log Analytics** pour les journaux agrégés avec fonction de requête avancée.

### <a name="log-streaming"></a>Diffusion de journaux

#### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/Azure-CLI)

Vous pouvez utiliser le streaming de journaux dans Azure CLI avec la commande suivante.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Vous verrez des journaux comme ceci :

[ ![Streaming de journaux depuis Azure CLI](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Utilisez `az spring-cloud app logs -h` pour explorer davantage de paramètres et de fonctionnalités du streaming de journaux.

Pour en savoir plus sur le langage de requête qui est utilisé dans Log Analytics, consultez [Requêtes de journal Azure Monitor](/azure/data-explorer/kusto/query/). Pour interroger tous vos journaux Log Analytics à partir d’un client centralisé, consultez [Explorateur de données Azure](/azure/data-explorer/query-monitor-data).

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Pour obtenir les journaux en utilisant Azure Toolkit for IntelliJ :

1. Sélectionnez **Azure Explorer**, puis **Spring Cloud**.

1. Cliquez avec le bouton droit sur l’application en cours d’exécution.

1. Sélectionnez **Journaux de streaming** dans la liste déroulante.

   ![Sélectionnez Journaux de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Sélectionnez **Instance**.

   ![Sélectionnez une instance](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Le journal de streaming est visible dans la fenêtre de sortie.

   ![Sortie de journal de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

 Pour en savoir plus sur le langage de requête qui est utilisé dans Log Analytics, consultez [Requêtes de journal Azure Monitor](/azure/data-explorer/kusto/query/). Pour interroger tous vos journaux Log Analytics à partir d’un client centralisé, consultez [Explorateur de données Azure](/azure/data-explorer/query-monitor-data).

---

### <a name="log-analytics"></a>Log Analytics

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Journaux** dans la section **Supervision**. Cliquez sur **Exécuter** sur un des exemples de requête pour Azure Spring Cloud.

   [ ![Entrée du portail Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png#lightbox)

1. Vous voyez alors les journaux filtrés. Pour plus d’informations sur l’écriture de requêtes, consultez [Documentation Azure Log Analytics](../azure-monitor/logs/get-started-queries.md).

   [ ![Requête Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Mesures

Accédez au panneau `Metrics` : vous pouvez voir les métriques fournies par les applications Spring Boot, les modules Spring Cloud et les dépendances. Le graphique ci-dessous montre `gateway-requests` (Spring Cloud Gateway), `hikaricp_connections` (connexions JDBC) et `http_client_requests`.
 
[ ![Panneau Métriques](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg#lightbox)

Spring Boot inscrit un grand nombre de métriques principales : JVM, CPU, Tomcat, Logback... La configuration automatique Spring Boot permet l’instrumentation des requêtes traitées par Spring MVC.
Ces trois contrôleurs REST `OwnerResource`, `PetResource` et `VisitResource` ont tous été instrumentés par l’annotation Micrometer `@Timed` au niveau de la classe.

* Les métriques personnalisées suivantes sont activées pour l’application `customers-service` :
  * @Timed: `petclinic.owner`
  * @Timed: `petclinic.pet`
* Les métriques personnalisées suivantes sont activées pour l’application `visits-service` :
  * @Timed: `petclinic.visit`

Vous pouvez voir ces métriques personnalisées dans le panneau `Metrics` : [ ![Métriques personnalisées](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg#lightbox)

Vous pouvez utiliser la fonctionnalité Test de disponibilité dans Application Insights et superviser la disponibilité des applications :

[ ![Test de disponibilité](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg#lightbox)

Accédez au panneau `Live Metrics` : vous pouvez voir les métriques en temps réel à l’écran avec des latences faibles inférieures à 1 seconde : [ ![Métrique temps réel](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg#lightbox)

## <a name="tracing"></a>Traçage

Ouvrez les insights d’application créés par Azure Spring Cloud, puis commencez à superviser les applications de microservices.

Accédez au panneau `Application Map` : [ ![Cartographie d’application](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg#lightbox)

Accédez au panneau `Performance` : [ ![Panneau des performances](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg#lightbox)

Accédez au panneau `Performance/Dependenices` : vous pouvez voir le nombre de performances pour les dépendances, notamment les appels SQL : [ ![Panneau des performances/dépendances](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg#lightbox)

Cliquez sur un appel SQL pour voir la transaction de bout en bout en contexte : [ ![Transaction SQL de bout en bout](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg#lightbox)

Accédez au panneau `Failures/Exceptions` :vous pouvez voir une collection d’exceptions : [ ![Échecs/Exceptions](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.jpg#lightbox)

Cliquez sur une exception pour voir la transaction de bout en bout et la trace de la pile en contexte : [ ![Trace de la pile de bout en bout](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans ces guides de démarrage rapide, vous avez créé des ressources Azure qui continueront de générer des frais tant qu’elles resteront dans votre abonnement. Si vous pensez que vous n’aurez pas besoin de ces ressources, supprimez le groupe de ressources dans le portail ou exécutez la commande suivante dans Cloud Shell :

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Dans un guide de démarrage rapide précédent, vous avez aussi défini le nom du groupe de ressources par défaut. Si vous ne prévoyez pas de passer au guide de démarrage rapide suivant, supprimez cette valeur par défaut en exécutant la commande CLI suivante :

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Étapes suivantes

Pour explorer les autres fonctionnalités de supervision Azure Spring Cloud, consultez :

> [!div class="nextstepaction"]
> [Services de diagnostic](diagnostic-services.md)
>
> [Traçage distribué](./how-to-distributed-tracing.md)
>
> [Effectuer le streaming des journaux en temps réel](./how-to-log-streaming.md)
