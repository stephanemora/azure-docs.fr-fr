---
title: Azure Monitor - zéro application instrumentation de surveillance pour Kubernetes hébergées applications | Microsoft Docs
description: Zéro application instrumentation de surveillance pour les applications Kubernetes hébergé est une solution de surveillance qui vous permet de collecter les données de télémétrie Application Insights se rapportant à des demandes entrantes et sortantes vers et depuis les pods en cours d’exécution dans votre cluster Kubernetes en utilisation de la technologie de maillage de service appelé Istio.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: 42b81ec0fa01841791a5b2651d1c1189db5e27ff
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408207"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Applications hébergées par zéro analyse des applications instrumentation pour Kubernetes

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor tire désormais parti tech de maillage de service sur votre cluster Kubernetes pour fournir en dehors de l’analyse des applications boîte pour n’importe quelle application Kubernetes hébergé. Valeur par défaut les fonctionnalités d’Application Insight comme [cartographie d’Application](../../azure-monitor/app/app-map.md) pour modéliser vos dépendances, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) pour l’analyse en temps réel, visualisations percutantes avec le [par défaut tableau de bord](../../azure-monitor/app/overview-dashboard.md), [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md), et [classeurs](../../azure-monitor/app/usage-workbooks.md). Cette fonctionnalité aidera les utilisateurs repérer les goulots d’étranglement et les zones réactives de défaillance dans l’ensemble de leurs charges de travail de Kubernetes dans un espace de noms Kubernetes sélectionné. En tirant parti de vos investissements de maillage de service existants des technologies telles que Istio, Azure Monitor permet la surveillance d’application instrumentée automatiquement sans aucune modification au code de votre application.

> [!NOTE]
> Il s’agit d’une des nombreuses façons d’effectuer l’analyse des applications sur Kubernetes. Vous pouvez également instrumenter toutes les applications hébergées dans Kubernetes à l’aide de la [SDK Application Insights](../../azure-monitor/azure-monitor-app-hub.md) sans avoir besoin d’une maille de services. Surveillance de Kubernetes sans l’instrumentation de l’application avec un kit de développement que vous pouvez utiliser la sous méthode.

## <a name="prerequisites"></a>Conditions préalables

- Un [cluster Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Accès au cluster pour exécuter la console *kubectl*.
- Un [ressource Application Insight](create-new-resource.md)
- Avoir une maille de services. Si votre cluster n’a pas Istio déployé, vous pouvez apprendre comment [installer et utiliser Istio dans Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Fonctionnalités

À l’aide de zéro application instrumentation de surveillance pour Kubernetes applications hébergées, vous serez en mesure d’utiliser :

- [Plan de l’application](../../azure-monitor/app/app-map.md)
- [Stream métriques temps réel](../../azure-monitor/app/live-stream.md)
- [Tableaux de bord](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Traçage distribué](../../azure-monitor/app/distributed-tracing.md)
- [Analyse des transactions de bout en bout](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Procédure d’installation :

Pour activer la solution, nous allons réaliser les étapes suivantes :
- Déployez l’application (si vous avez pas encore déployé).
- Vérifiez que l’application fait partie de la maille de services.
- Observez les données de télémétrie collectées.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurer votre application pour travailler avec une maille de services

Istio prend en charge deux méthodes de [instrumentation d’un pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Dans la plupart des cas, il est plus simple marquer l’espace de noms Kubernetes contenant votre application avec le *istio injection* étiquette :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Dans la mesure où les données de service maillage ascenseurs désactivé le câble, nous ne pouvons pas intercepter le trafic chiffré. Pour le trafic qui ne quittent pas le cluster, utilisez un protocole non chiffré (par exemple, HTTP). Pour le trafic externe qui doit être chiffré, envisagez [configuration d’un arrêt SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) sur le contrôleur d’entrée.

Applications qui s’exécutent en dehors de la maille de services ne sont pas affectées.

### <a name="deploy-your-application"></a>Déployer votre application

- Déployer votre application sur *-application-espace de noms my* espace de noms. Si l’application est déjà déployée et que vous avez suivi la méthode de l’injection automatique side-car décrite ci-dessus, vous devez recréer les pods pour vous assurer Qu'istio injecte ses side-car ; soit lancer une mise à jour propagée ou supprimer des pods individuels et attendre pour qu’ils puissent être recréé.
- Vérifiez votre application est compatible avec [Istio exigences](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Déployer zéro application instrumentation de surveillance pour Kubernetes applications hébergées

1. Téléchargez et extrayez un [ *adaptateur d’Application Insights* release](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Accédez à */src/kubernetes/* dans le dossier de mise en production.
3. Edit *application-insights-istio-mixer-adapter-deployment.yaml*
    - modifier la valeur de *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variable d’environnement pour contenir la clé d’instrumentation de la ressource Application Insights dans le portail Azure pour contenir les données de télémétrie.
    - Si nécessaire, modifiez la valeur de *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variable d’environnement contient une liste séparée par des virgules des espaces de noms pour lequel vous souhaitez activer la surveillance. Laisser vide pour surveiller tous les espaces de noms.
4. Appliquer *chaque* fichier YAML se trouve sous *src/kubernetes/* en exécutant la commande suivante (vous devez toujours être à l’intérieur de */src/kubernetes/*) :

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Vérifier le déploiement

- Vérifiez l’adaptateur d’Application Insights a été déployé :

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Dans certains cas, le réglage de réglage est nécessaire. Pour inclure ou exclure les données de télémétrie pour un pod individuel d’être collecté, utilisez *appinsights/monitoring.enabled* étiquette sur ce bloc. Cela aura priorité sur toute la configuration basée sur l’espace de noms. Définissez *appinsights/monitoring.enabled* à *true* pour inclure le pod et à *false* pour l’exclure.

### <a name="view-application-insights-telemetry"></a>Afficher les données de télémétrie Application Insights

- Générer un exemple de demande à votre application pour vérifier que la surveillance fonctionne correctement.
- Dans les 3 à 5 minutes, vous devez commencer voir les données de télémétrie s’affichent dans le portail Azure. Veillez à consulter le *cartographie d’Application* section de votre ressource Application Insights dans le portail.

## <a name="troubleshooting"></a>Résolution de problèmes

Ci-dessous le flux de dépannage à utiliser lors de la télémétrie n’apparaît pas dans le portail Azure en tant qu’est attendu.

1. Vérifiez que l’application est sous charge et est envoi/réception de demandes HTTP brut. Dans la mesure où les données de télémétrie sont levée du câble, le trafic chiffré n’est pas pris en charge. S’il n’y a aucune demande entrant ou sortant, il n’y aura aucune télémétrie soit.
2. Vérifiez la clé d’instrumentation correcte est fournie dans le *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variable d’environnement dans *application-insights-istio-mixer-adapter-deployment.yaml*. La clé d’instrumentation se trouve sur le *vue d’ensemble* onglet de la ressource Application Insights dans le portail Azure.
3. Vérifiez l’espace de noms Kubernetes correct est fourni dans le *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variable d’environnement dans *application-insights-istio-mixer-adapter-deployment.yaml*. Laisser vide pour surveiller tous les espaces de noms.
4. Vérifiez que les pods de votre application ont été injectés side-car par Istio. Vérifiez que les side-car d’Istio existe sur chaque bloc.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Vérifiez qu’il existe un conteneur nommé *istio-proxy* en cours d’exécution sur le pod.

5. Afficher les traces de l’adaptateur d’Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Le nombre d’éléments de télémétrie reçu est mis à jour une fois par minute. Si elle n’augmente pas minute par minute - aucune télémétrie n’est envoyée à l’adaptateur par Istio.
   Recherchez les erreurs dans le journal.
6. Si elle a été établie qui *Application Insight pour Kubernetes* adaptateur n’est pas chargé télémétrie, consultez les journaux de Mixer d’Istio pour déterminer la raison pour laquelle il pas envoie des données à l’adaptateur :

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Recherchez les erreurs, notamment concernant les communications avec *applicationinsightsadapter* adaptateur.

## <a name="faq"></a>Forum Aux Questions

Pour obtenir les dernières informations pour connaître la progression sur ce projet, visitez le [adaptateur d’Application Insights pour les GitHub du projet Istio Mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Désinstaller l’interface

Pour désinstaller le produit, pour *chaque* fichier YAML se trouve sous *src/kubernetes/* exécuter :

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Monitor et les conteneurs fonctionnement conjoint visite [Azure Monitor pour une vue d’ensemble de conteneurs](../../azure-monitor/insights/container-insights-overview.md)