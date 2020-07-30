---
title: Utilisez Application Insights pour surveiller votre service Azure Kubernetes (AKS) ou d’autres application Kubernetes hébergées - Azure Monitor | Microsoft Docs
description: Azure Monitor utilise la technologie de maillage de service (Istio) sur votre cluster Kubernetes pour fournir une surveillance des applications pour n’importe quelle application Kubernetes hébergée. Cela vous permet de collecter des données de télémétrie Application Insights se rapportant aux requêtes entrantes et sortantes vers et depuis les pods en cours d’exécution dans votre cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3cd43963175594fcdc1c3c67d6b2493ce1ccd313
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321919"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Surveillance d’applications sans instrumentation pour les applications hébergées Kubernetes avec Istio – DÉCONSEILLÉ

> [!IMPORTANT]
> Cette fonctionnalité est actuellement déconseillée et ne sera plus prise en charge après le 1er août 2020.
> Actuellement, la surveillance sans code ne peut être activée que pour [Java par le biais d’un agent autonome](./java-in-process-agent.md). Pour les autres langages, utilisez les kits de développement logiciel (SDK) pour surveiller vos applications sur AKS : [ASP.Net Core](./asp-net-core.md), [ASP.Net](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md) et [Python](./opencensus-python.md).

Azure Monitor tire maintenant parti de la technologie de maillage de service sur votre cluster Kubernetes pour fournir une surveillance immédiate des applications pour n’importe quelle application Kubernetes hébergée. Avec les fonctionnalités d’Application Insight par défaut comme [Application Map](./app-map.md) pour modéliser vos dépendances, [Live Metrics Stream](./live-stream.md) pour la surveillance en temps réel, les visualisations percutantes avec le [tableau de bord par défaut](./overview-dashboard.md), [Metrics Explorer](../platform/metrics-getting-started.md), et [Workbooks](../platform/workbooks-overview.md). Cette fonctionnalité aide les utilisateurs à repérer les goulots d’étranglement et les zones réactives de défaillance dans l’ensemble de leurs charges de travail Kubernetes au sein d’un espace de noms Kubernetes sélectionné. En tirant parti de vos investissements de maillage de service existants avec des technologies telles que Istio, Azure Monitor permet la surveillance d’application instrumentée automatiquement sans modification du code de votre application.

> [!NOTE]
> Il s’agit d’une des nombreuses façons de surveiller les applications sur Kubernetes. Vous pouvez également instrumenter toutes les applications hébergées dans Kubernetes à l’aide du [kit SDK Application Insights](../azure-monitor-app-hub.yml) sans avoir besoin d’une maille de services. Vous pouvez utiliser la méthode suivante pour surveiller Kubernetes sans instrumentation de l’application avec un kit SDK.

## <a name="prerequisites"></a>Prérequis

- Un [cluster Kubernetes](../../aks/concepts-clusters-workloads.md).
- Accès au cluster par une console pour exécuter *kubectl*.
- Une [ressource Application Insight](create-new-resource.md)
- Disposer d’une maille de services. Si Istio n’est pas déployé sur votre cluster, vous pouvez apprendre à [installer et utiliser Istio dans Azure Kubernetes Service](../../aks/servicemesh-istio-install.md).

## <a name="capabilities"></a>Fonctionnalités

À l’aide de la surveillance d’applications sans instrumentation pour les applications hébergées Kubernetes, vous serez en mesure d’utiliser :

- [Plan de l’application](./app-map.md)
- [Live Metrics Stream](./live-stream.md)
- [Tableaux de bord](./overview-dashboard.md)
- [Metrics Explorer](../platform/metrics-getting-started.md)
- [Le traçage distribué](./distributed-tracing.md)
- [La surveillance des transactions de bout en bout](../learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Procédure d’installation :

Pour activer la solution, nous allons suivre les étapes suivantes :
- Déployer l’application (si ce n’est pas déjà fait).
- Vérifier que l’application fait partie de la maille de services.
- Observer les données de télémétrie collectées.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurer votre application pour qu’elle fonctionne avec une maille de services

Istio prend en charge deux méthodes d’[instrumentation de pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Dans la plupart des cas, il est plus simple de marquer l’espace de noms Kubernetes contenant votre application avec l’étiquette *istio injection* :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Dans la mesure où la maille de service retire les données du fil, nous ne pouvons pas intercepter le trafic chiffré. Pour le trafic qui ne quitte pas le cluster, utilisez un protocole non chiffré (le protocole HTTP par exemple). Pour le trafic externe qui doit être chiffré, envisagez de [configurer un arrêt TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) sur le contrôleur d’entrée.

Les applications exécutées en dehors de la maille de services ne sont pas affectées.

### <a name="deploy-your-application"></a>Déployer votre application

- Déployez votre application sur un espace de noms *my-app-namespace*. Si l’application est déjà déployée et que vous avez suivi la méthode d’injection automatique side-car décrite ci-dessus, vous devez recréer les pods pour vous assurer qu’Istio injecte ses side-car ; soit en lançant une mise à jour propagée ou bien en supprimant des pods individuels et en attenant qu’ils soient recréés.
- Vérifiez que votre application répond aux [exigences d’Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Déployer une surveillance d’applications sans instrumentation pour les applications hébergées Kubernetes

1. Téléchargez et extrayez une mise à jour de [*l’adaptateur d’Application Insights*](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Accédez à */src/kubernetes/* dans le dossier de mise à jour .
3. Modifiez *application-insights-istio-mixer-adapter-deployment.yaml*
    - Modifier la valeur de la variable d’environnement *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* pour contenir la clé d’instrumentation de la ressource Application Insights dans le portail Azure pour contenir les données de télémétrie.
    - Si nécessaire, modifiez la valeur de la variable d’environnement *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* pour qu’elle contienne une liste des espaces de noms séparés par des virgules, pour lesquels vous souhaiteriez activer la surveillance. Laissez-la vide pour surveiller tous les espaces de noms.
4. Appliquez *chaque* fichier YAML trouvé dans le dossier *src/kubernetes/* en exécutant la commande suivante (vous devez toujours être dans le dossier */src/kubernetes/* ) :

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Vérifier le déploiement

- Vérifiez que l’adaptateur d’Application Insights a été déployé :

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Dans certains cas, un réglage fin est nécessaire. Pour inclure ou exclure la collecte des données de télémétrie pour un pod individuel, utilisez l’étiquette *appinsights/monitoring.enabled* sur dessus. Elle aura priorité sur toute configuration basée sur l’espace de noms. Définissez *appinsights/monitoring.enabled* sur *true* pour inclure le pod et sur *false* pour l’exclure.

### <a name="view-application-insights-telemetry"></a>Voir Application Insights Telemetry

- Générez un exemple de demande pour votre application afin de vérifier que la surveillance fonctionne correctement.
- Dans les 3 à 5 minutes, vous devriez commencer voir les données de télémétrie apparaître dans le portail Azure. Veillez à consulter la section *cartographie d’Application* de votre ressource Application Insights dans le portail.

## <a name="troubleshooting"></a>Dépannage

Vous trouverez ci-dessous le flux de dépannage à utiliser lorsque les données de télémétrie n’apparaissent pas dans le portail Azure comme attendu.

1. Vérifiez que l’application est en charge et qu’elle envoie/reçoit des demandes HTTP brut. Dans la mesure où les données de télémétrie sont levée du câble, le trafic chiffré n’est pas pris en charge. S’il n’y a aucune demande entrante ou sortante, il n’y aura aucune donnée de télémétrie.
2. Vérifiez la clé d’instrumentation correcte est fournie dans la variable d’environnement *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* au sein du fichier *application-insights-istio-mixer-adapter-deployment.yaml*. La clé d’instrumentation se trouve dans l’onglet *Vue d’ensemble* de la ressource Application Insights dans le portail Azure.
3. Vérifiez l’espace de noms Kubernetes correct est fourni dans la variable d’environnement *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* au sein du fichier *application-insights-istio-mixer-adapter-deployment.yaml*. Laissez-la vide pour surveiller tous les espaces de noms.
4. Vérifiez que les pods de votre application ont été injectés en side-car par Istio. Vérifiez que le side-car d’Istio existe sur chaque pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Vérifiez qu’il existe un conteneur nommé *istio-proxy* en cours d’exécution sur le pod.

5. Affichez les traces de l’adaptateur Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Le nombre d’éléments de télémétrie reçu est mis à jour chaque minute. S’il n’augmente pas chaque minute, cela signifie qu’aucune donnée de télémétrie n’est envoyée à l’adaptateur par Istio.
   Recherchez les erreurs dans le journal.
6. S’il a été établi que l’adaptateur*Application Insight pour Kubernetes* ne reçoit pas de données de télémétrie, consultez les journaux Mixer d’Istio pour déterminer la raison pour laquelle il n’envoie pas de données à l’adaptateur :

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Recherchez les erreurs, notamment celles concernant les communications avec l’adaptateur *applicationinsightsadapter*.

## <a name="faq"></a>Questions fréquentes (FAQ)

Pour obtenir les dernières informations sur la progression de ce projet, visitez la page [GitHub de l’adaptateur Application Insights pour le projet Istio Mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Désinstaller l’interface

Pour désinstaller le produit, exécutez la commande ci-dessous pour *chaque* fichier YAML se trouvant dans *src/kubernetes/*  :

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Étapes suivantes

Pour découvrir Azure Monitor et les conteneurs fonctionnent ensemble, consultez la [Vue d’ensemble d’Azure Monitor pour les conteneurs](../insights/container-insights-overview.md)

