---
title: Démarrage rapide - Supervision des applications Azure Spring Cloud avec les journaux, les métriques et le suivi
description: Utilisez le streaming des journaux, Log Analytics, les métriques et le suivi pour superviser les exemples d’applications Piggymetrics sur Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046831"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Démarrage rapide : Supervision des applications Azure Spring Cloud avec les journaux, les métriques et le suivi

Avec les fonctionnalités de supervision intégrées d’Azure Spring Cloud, vous pouvez déboguer et superviser des problèmes complexes. Azure Spring Cloud intègre Azure [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) au service [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) d’Azure. Cette intégration fournit des fonctionnalités puissantes en matière de journaux, de métriques et de suivi distribué, disponibles dans le portail Azure. Les procédures suivantes expliquent comment utiliser le streaming de journaux, Log Analytics, les métriques et le suivi distribué avec les applications PiggyMetrics déployées.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes précédentes : 

* [Provisionner une instance d’Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Configurer le serveur de configuration](spring-cloud-quickstart-setup-config-server.md)
* [Créer et déployer des applications](spring-cloud-quickstart-deploy-apps.md)

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

---
### <a name="log-analytics"></a>Log Analytics

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Journaux** dans la section **Supervision**. Cliquez sur **Exécuter** sur un des exemples de requête pour Azure Spring Cloud. 

   [ ![Entrée Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Vous voyez alors les journaux filtrés. Pour plus d’informations sur l’écriture de requêtes, consultez [Documentation Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries).

   [ ![Requête Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Mesures

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Métriques** dans la section **Supervision**. Ajoutez votre première métrique en sélectionnant `system.cpu.usage` pour **Métrique** et `Avg` pour **Agrégation** pour voir la chronologie de l’utilisation globale du processeur.

   [ ![Entrée Métriques](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Cliquez sur **Ajouter un filtre** dans la barre d’outils ci-dessus, sélectionnez `App=Gateway` pour voir l’utilisation du processeur seulement pour l’application **gateway**.

   [ ![Utiliser un filtre dans les métriques](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Abandonnez le filtre créé ci-dessus, cliquez sur **Appliquer la division**, puis sélectionnez `App` pour **Valeurs** afin de voir l’utilisation du processeur pour les différentes applications.

   [ ![Appliquer la division dans les métriques](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Traçage distribué

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Suivi distribué** dans la section **Supervision**. Cliquez ensuite sur l’onglet **Afficher la cartographie d’application** sur la droite.

   [ ![Entrée Suivi distribué](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Vous pouvez maintenant voir l’état des appels entre les applications Piggymetrics. 

   [ ![Vue d’ensemble du suivi distribué](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Cliquez sur le lien entre **gateway** et **account-service** pour voir plus de détails, comme que les appels les plus lents pour les méthodes HTTP.

   [ ![Suivi distribué](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Enfin, cliquez sur **Examiner les performances** pour explorer plus en détail l’analyse des performances intégrée.

   [ ![Suivi distribué - Performances](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources plus tard, supprimez le groupe de ressources du portail en exécutant la commande suivante dans Cloud Shell :

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Dans les étapes précédentes, vous avez aussi défini le nom du groupe de ressources par défaut. Pour supprimer cette valeur par défaut, exécutez la commande suivante dans Cloud Shell :

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Étapes suivantes

Pour explorer plus en détail les fonctionnalités de supervision prêtes à l’emploi pour Azure Spring Cloud, consultez :

> [!div class="nextstepaction"]
> [Services de diagnostic](diagnostic-services.md)
> [Suivi distribué](spring-cloud-tutorial-distributed-tracing.md)
> [Effectuer le streaming de journaux en temps réel](spring-cloud-howto-log-streaming.md)
