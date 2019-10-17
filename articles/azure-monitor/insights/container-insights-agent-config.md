---
title: Configuration d’Azure Monitor pour la collecte de données de l’agent de conteneurs | Microsoft Docs
description: Cet article décrit comment configurer Azure Monitor pour que l’agent de conteneurs contrôle stdout/stderr et la collecte des journaux de variables d’environnement.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: ada573cc919d775af52abc5a75004866aebbeddb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72033940"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurer la collecte de données de l’agent pour Azure Monitor pour conteneurs

Azure Monitor pour conteneurs collecte stdout, stderr et des variables d’environnement à partir de charges de travail de conteneur déployées sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS) à partir de l’agent conteneurisé. Cet agent peut également collecter des données de série chronologique (également appelées métriques) à partir de Prometheus à l’aide de l’agent conteneurisé sans avoir à configurer et à gérer un serveur et une base de données Prometheus. Vous pouvez configurer les paramètres de collecte de données de l’agent en créant une ConfigMaps Kubernetes personnalisée pour contrôler cette expérience. 

Cet article montre comment créer la ConfigMap et configurer la collecte de données selon vos besoins.

>[!NOTE]
>La prise en charge de Prometheus est une fonctionnalité de la préversion publique actuellement.
>

## <a name="configmap-file-settings-overview"></a>Vue d’ensemble des paramètres de fichier ConfigMap

Un fichier ConfigMap de modèle est fourni. Vous pouvez le modifier facilement avec vos personnalisations sans avoir à le créer à partir de zéro. Avant de commencer, vous devez consulter la documentation Kubernetes sur [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) et vous familiariser avec la manière de créer, de configurer et de déployer ConfigMaps. Cela vous permet de filtrer stderr et stdout par espace de noms ou dans l’ensemble du cluster et les variables d’environnement pour les conteneurs en cours d’exécution sur tous les nœuds/pods du cluster.

>[!IMPORTANT]
>La version minimale de l’agent prise en charge pour collecter des variables stdout, stderr et environnementales à partir de charges de travail de conteneur est ciprod06142019 ou ultérieure. La version minimale de l’agent prise en charge pour la capture des mesures Prometheus est ciprod07092019 ou ultérieure. Pour vérifier la version de votre agent, sous l’onglet **Nœud**, sélectionnez un nœud, puis dans la valeur note du volet Propriétés de la propriété **Balise d’image de l’agent**.  

### <a name="data-collection-settings"></a>Paramètres de collecte de données

Voici les paramètres qui peuvent être configurés pour contrôler la collecte de données.

|Clé |Type de données |Valeur |Description |
|----|----------|------|------------|
|`schema-version` |Chaîne (respecte la casse) |v1 |Il s’agit de la version de schéma utilisée par l’agent lors de l’analyse de cette ConfigMap. Actuellement, la version prise en charge est v1. Modifier cette valeur n’est pas pris en charge et est rejeté lors de l’évaluation de l’élément ConfigMap.|
|`config-version` |Chaîne | | Prend en charge la possibilité d’effectuer le suivi de la version de ce fichier de configuration dans votre système/dépôt de contrôle de code source. Le nombre maximal de caractères autorisé est 10 et tous les autres caractères sont tronqués. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true ou false | Ce paramètre contrôle si la collecte de journaux de conteneur stdout est activée. Lorsque la valeur est `true` et qu’aucun espace de noms n’est exclus de la collecte de journaux stdout (paramètre `log_collection_settings.stdout.exclude_namespaces` ci-dessous), les journaux stdout sont collectés à partir de tous les conteneurs parmi l’ensemble des nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMaps, la valeur par défaut est `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Chaîne | Tableau séparé par des virgules |Tableau d’espaces de noms Kubernetes pour lesquels aucun journal stdout n’est collecté. Ce paramètre est effectif uniquement si `log_collection_settings.stdout.enabled` est défini sur `true`. S’il n’est pas spécifié dans ConfigMap, la valeur par défaut est `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true ou false |Ce paramètre contrôle si la collecte de journaux de conteneur stderr est activée. Lorsque la valeur est `true` et qu’aucun espace de noms n’est exclus de la collecte de journaux stdout (paramètre `log_collection_settings.stderr.exclude_namespaces`), les journaux stderr sont collectés à partir de tous les conteneurs parmi l’ensemble des nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMaps, la valeur par défaut est `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Chaîne |Tableau séparé par des virgules |Tableau d’espaces de noms Kubernetes pour lesquels aucun journal stderr n’est collecté. Ce paramètre est effectif uniquement si `log_collection_settings.stdout.enabled` est défini sur `true`. S’il n’est pas spécifié dans ConfigMap, la valeur par défaut est `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true ou false | Ce paramètre contrôle la collecte des variables d’environnement sur tous les pods/nœuds du cluster et a pour valeur par défaut `enabled = true` lorsqu’il n’est pas spécifié dans ConfigMaps. Si la collecte des variables d’environnement est globalement activée, vous pouvez la désactiver pour un conteneur spécifique en définissant la variable d’environnement `AZMON_COLLECT_ENV` sur **False** avec un paramètre Dockerfile ou dans le [fichier de configuration pour le pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) dans la section **env :** . Si la collecte des variables d’environnement est désactivée globalement, vous ne pouvez pas activer la collecte pour un conteneur spécifique (autrement dit, la seule substitution applicable au niveau du conteneur consiste à désactiver la collecte lorsqu’elle est déjà activée globalement). |

### <a name="prometheus-scraping-settings"></a>Paramètres de capture de Prometheus

![Architecture de surveillance de conteneur pour Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

Azure Monitor pour conteneurs fournit une expérience continue permettant la collecte des métriques Prometheus à l’aide de plusieurs captures via les mécanismes suivants, indiqués dans le tableau ci-dessous. Les métriques sont collectées par le biais d’un ensemble de paramètres spécifiés dans un fichier ConfigMap unique, qui est le même que celui utilisé pour configurer la collecte des variables stdout, stderr et d’environnement à partir de charges de travail de conteneur. 

La capture active de métriques à partir de Prometheus est effectuée de l’une des deux perspectives suivantes :

* À l’ensemble du cluster - URL HTTP et découverte des cibles à partir des points de terminaison listés d’un service, services K8S tels que Kube-DNS et Kube-State-Metrics, et les annotations pod spécifiques à une application. Les mesures collectées dans ce contexte sont définies dans la section ConfigMap *[Prometheus data_collection_settings.cluster]* .
* À l’ensemble du nœud - URL HTTP et découverte des cibles à partir des points de terminaison listés d’un service. Les mesures collectées dans ce contexte sont définies dans la section ConfigMap *[Prometheus data_collection_settings.node]* .

| Point de terminaison | Étendue | Exemples |
|----------|-------|---------|
| Annotation de pod | À l’ensemble du cluster | annotations : <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Service Kubernetes | À l’ensemble du cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/point de terminaison | Par nœud et/ou ensemble du cluster | `http://myurl:9101/metrics` |

Quand une URL est spécifiée, Azure Monitor pour conteneurs ne capture que le point de terminaison. Quand le service Kubernetes est spécifié, le nom du service est résolu avec le serveur DNS du cluster pour obtenir l’adresse IP, puis le service résolu est capturé.

|Étendue | Clé | Type de données | Valeur | Description |
|------|-----|-----------|-------|-------------|
| À l’ensemble du cluster | | | | Spécifiez l’une des trois méthodes suivantes pour capturer les points de terminaison pour les mesures. |
| | `urls` | Chaîne | Tableau séparé par des virgules | Point de terminaison HTTP (adresse IP ou chemin d’URL valide spécifié). Par exemple : `urls=[$NODE_IP/metrics]`. ($NODE_IP est un paramètre Azure Monitor spécifique pour conteneurs et peut être utilisé à la place d’une adresse IP de nœud. Doit être tout en majuscules.) |
| | `kubernetes_services` | Chaîne | Tableau séparé par des virgules | Tableau de services Kubernetes pour la capture des mesures à partir des kube-state-metrics. Par exemple : `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | true ou false | Lorsque la valeur `true` est définie sur dans les paramètres du cluster, Azure Monitor de l’agent des conteneurs capture les pod Kubernetes sur l’ensemble du cluster pour les annotations Prometheus suivantes :<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true ou false | Permet la capture du pod. `monitor_kubernetes_pods` doit être défini sur `true`. |
| | `prometheus.io/scheme` | Chaîne | http ou https | La valeur par défaut est la capture de HTTP. Si nécessaire, affectez la valeur `https`. | 
| | `prometheus.io/path` | Chaîne | Tableau séparé par des virgules | Chemin d’accès de la ressource HTTP à partir duquel récupérer les mesures. Si le chemin d’accès aux mesures n’est pas `/metrics`, définissez-le avec cette annotation. |
| | `prometheus.io/port` | Chaîne | 9102 | Spécifiez le port local à écouter. Si le port n’est pas défini, sa valeur par défaut est 9102. |
| À l’ensemble du nœud | `urls` | Chaîne | Tableau séparé par des virgules | Point de terminaison HTTP (adresse IP ou chemin d’URL valide spécifié). Par exemple : `urls=[$NODE_IP/metrics]`. ($NODE_IP est un paramètre Azure Monitor spécifique pour conteneurs et peut être utilisé à la place d’une adresse IP de nœud. Doit être tout en majuscules.) |
| À l’ensemble du nœud ou du cluster | `interval` | Chaîne | 60s | La valeur par défaut de l’intervalle de collection est d’une minute (60 secondes). Vous pouvez modifier la collection pour *[prometheus_data_collection_settings. node]* et/ou pour *[prometheus_data_collection_settings. cluster]* en unités de temps telles que NS, US (ou Âμs), MS, s, m, h. |
| À l’ensemble du nœud ou du cluster | `fieldpass`<br> `fielddrop`| Chaîne | Tableau séparé par des virgules | Vous pouvez spécifier certaines mesures à collecter ou non à partir du point de terminaison en définissant la liste Autoriser (`fieldpass`) et Interdire (`fielddrop`). Vous devez d’abord définir la liste Autoriser. |

ConfigMaps est une liste globale et il ne peut y avoir qu’un seul élément ConfigMap appliqué à l’agent. Vous ne pouvez pas avoir un autre élément ConfigMaps qui annule les collectes.

## <a name="configure-and-deploy-configmaps"></a>Configurer et déployer ConfigMaps

Procédez comme suit pour configurer et déployer votre fichier de configuration ConfigMap dans votre cluster.

1. [Téléchargez](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) le fichier yaml ConfigMap de modèle et enregistrez-le sous le nom container-azm-ms-agentconfig.yaml.  
1. Modifiez le fichier yaml ConfigMap avec vos personnalisations.

    - Pour exclure des espaces de noms spécifiques pour la collecte de journaux stdout, configurez la clé/valeur à l’aide de l’exemple suivant : `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Pour désactiver la collecte de variables d’environnement pour un conteneur spécifique, définissez la clé/valeur `[log_collection_settings.env_var] enabled = true` pour activer la collecte de variables globalement, puis suivez les étapes décrites [ici](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) pour terminer la configuration pour le conteneur spécifique.
    
    - Pour désactiver la collecte de journaux stderr au niveau du cluster, configurez la clé/valeur à l’aide de l’exemple suivant : `[log_collection_settings.stderr] enabled = false`.
    
    - Les exemples suivants montrent comment configurer les métriques de fichier ConfigMap à partir d’une URL à l’ensemble du cluster, d’un DameonSet à l’ensemble du nœud et en spécifiant une annotation de pod

        - Capturer les métriques Prometheus à partir d’une URL spécifique sur le cluster.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - Capturer les métriques Prometheus à partir du DaemonSet d’un agent en cours d’exécution dans chaque nœud du cluster.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - Capturer les métriques Prometheus en spécifiant une annotation de pod.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. Créez un ConfigMap en exécutant la commande kubectl suivante : `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemple : `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Quelques minutes peuvent être nécessaires pour que la modification de configuration soit effective. Ensuite, tous les pods omsagent du cluster redémarrent. Le redémarrage s’effectue de façon progressive pour tous les pods omsagent. Tous ne redémarrent pas en même temps. Lorsque les redémarrages sont terminés, un message similaire à celui-ci s’affiche avec les résultats : `configmap "container-azm-ms-agentconfig" created`.

Pour vérifier que la configuration a été correctement appliquée, utilisez la commande suivante pour passer en revue les journaux à partir d’un pod d’agent : `kubectl logs omsagent-fdf58 -n=kube-system`. S’il existe des erreurs de configuration à partir de pods osmagent, la sortie affiche des erreurs similaires à ce qui suit :

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Des erreurs liées à l’application des modifications de configuration pour Prometheus sont également disponibles pour évaluation.  Soit à partir des journaux d’un pod d’agent à l’aide de la même commande `kubectl logs`, soit à partir de journaux dynamiques. Les journaux dynamiques affichent des erreurs similaires à ce qui suit :

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Des erreurs empêchent omsagent d’analyser le fichier, ce qui provoque son redémarrage et l’utilisation de la configuration par défaut. Une fois que vous avez corrigé les erreurs dans ConfigMap, enregistrez le fichier yaml et appliquez la ConfigMaps mise à jour en exécutant la commande : `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Application du ConfigMap mis à jour

Si vous avez déjà déployé un ConfigMap dans votre cluster et que vous souhaitez le mettre à jour avec une configuration plus récente, vous pouvez modifier le fichier ConfigMap que vous utilisiez précédemment, puis l’appliquer à l’aide de la même commande qu’avant, `kubectl apply -f <configmap_yaml_file.yaml`.

Quelques minutes peuvent être nécessaires pour que la modification de configuration soit effective. Ensuite, tous les pods omsagent du cluster redémarrent. Le redémarrage s’effectue de façon progressive pour tous les pods omsagent. Tous ne redémarrent pas en même temps. Lorsque les redémarrages sont terminés, un message similaire à celui-ci s’affiche avec les résultats : `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Vérification de la version du schéma

Les versions de schéma de configuration pris en charge sont disponibles en tant qu’annotation de pod (versions de schéma) sur le pod omsagent. Vous pouvez les voir avec la commande kubectl suivante : `kubectl describe pod omsagent-fdf58 -n=kube-system`

La sortie qui s’affiche est similaire à ce qui suit avec les versions de schéma d’annotation :

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>Examinez l’utilisation des données Prometheus

Pour voir les métriques Prometheus récupérées par Azure Monitor, spécifiez « prometheus » comme espace de noms. Voici un exemple de requête permettant de voir les métriques Prometheus à partir de l’espace de noms kubernetes `default`.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| extend tags=parse_json(Tags)
| where tostring(tags.namespace) == "default" 
```

Les données Prometheus peuvent également être interrogées directement par nom.

```
InsightsMetrics 
| where Name contains "some_prometheus_metric"
```

Pour identifier le volume d’ingestion de chaque taille de mesure en Go par jour pour déterminer si elle est élevée, la requête suivante est fournie.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
La sortie indiquera des résultats similaires à ce qui suit :

![Consignez les résultats de requête du volume d’ingestion de données](./media/container-insights-agent-config/log-query-example-usage-03.png)

Pour estimer la taille de chaque mesure en Go pendant un mois pour déterminer si le volume de données reçues dans l’espace de travail est élevé, la requête suivante est fournie.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

La sortie indiquera des résultats similaires à ce qui suit :

![Consignez les résultats de requête du volume d’ingestion de données](./media/container-insights-agent-config/log-query-example-usage-02.png)

Vous trouverez plus d’informations sur la surveillance de l’utilisation des données et l’analyse des coûts dans [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Étapes suivantes

Azure Monitor pour conteneurs n’inclut pas d’un ensemble prédéfini d’alertes. Pour savoir comment créer les alertes recommandées pour une utilisation élevée du processeur et de la mémoire, consultez [Créer des alertes de performances avec Azure Monitor pour conteneurs](container-insights-alerts.md).

- Pour continuer à découvrir comment utiliser Azure Monitor et surveiller les autres aspects de votre cluster AKS, consultez [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).

- Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.
