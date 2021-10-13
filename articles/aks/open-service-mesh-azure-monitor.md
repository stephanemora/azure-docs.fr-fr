---
title: Utilisation d’Azure Monitor et Application Insights
description: Utilisation d’Azure Monitor et Application Insights avec Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 4e1df1275e225b42bf01bd2a4092cea07f9c4c12
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057811"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Surveillance et observabilité d’Open Service Mesh (OSM) à l’aide d’Azure Monitor et Applications Insights

Azure Monitor et Azure Application Insights aident à optimiser la disponibilité et les performances de vos applications et services. Ces services offrent une solution complète pour collecter, analyser et exploiter les données de télémétrie de vos environnements cloud et locaux.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Le module complémentaire OSM AKS sera profondément intégré à ces deux services Azure et fournira une expérience Azure transparente permettant de visualiser les indicateurs clés de performance essentiels fournis par les métriques d’OSM et d’y répondre. 

## <a name="enable-azure-monitor"></a>Activer Azure Monitor

Une fois que le module complémentaire AKS OSM a été activé sur le cluster AKS, Azure Monitor doit être activé dans le cluster via le portail Azure. Cliquez sur le cluster AKS, accédez à l’onglet « Informations » sous « Supervision », puis sélectionnez « Activer ». 

Une fois Azure Monitor activé, vous devriez être en mesure de voir les journaux suivants dans l’espace de noms kube-system : 

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>Activer les métriques dans les espaces de noms analysés par OSM

Pour les métriques à récupérer à partir d’un espace de noms spécifique surveillé par la maille, la commande suivante doit être exécutée :

```sh
osm metrics enable --osm-namespace <namespace>
```

Par exemple, si vous exécutez la [démonstration Bookstore](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/), vous devez exécuter la commande `osm metrics enable` sur les espaces de noms suivants :

```sh
osm metrics enable --osm-namespace bookbuyer
osm metrics enable --osm-namespace bookstore
osm metrics enable --osm-namespace bookthief
osm metrics enable --osm-namespace bookwarehouse
```
## <a name="apply-configmaps"></a>Appliquer ConfigMaps

Créez l’élément ConfigMap suivant dans `kube-system`, ce qui indiquera à AzMon quels espaces de noms doivent être surveillés. Par exemple, pour la démonstration Bookbuyer/Bookstore, l’élément ConfigMap se présente comme suit : 

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system

```

Ensuite, vous devez créer un deuxième élément ConfigMap pour définir [monitor_kubernetes_pods sur true](https://github.com/microsoft/Docker-Provider/blob/24b709f9e3c3b18779102b491fc98b87a99d1335/kubernetes/container-azm-ms-agentconfig.yaml#L72).

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: container-azm-ms-agentconfig
  namespace: kube-system
data:
  schema-version: v1
  config-version: ver1
  prometheus-data-collection-settings: |-
    [prometheus_data_collection_settings.cluster]
        interval = "30s"
        monitor_kubernetes_pods = true
```

## <a name="view-metrics-in-the-azure-portal"></a>Afficher les métriques dans le portail Azure

Dans le portail Azure, sélectionnez le cluster Kubernetes, puis l’onglet « Journaux » sous « Supervision ». Vous devriez maintenant être en mesure d’interroger la table `InsightsMetrics` pour afficher les métriques dans les espaces de noms activés. Par exemple, si vous souhaitez afficher les métriques d’envoi pour `bookbuyer`, vous devez utiliser la requête suivante :

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>Informations supplémentaires

Pour plus d’informations sur l’activation et la configuration d’Azure Monitor et Azure Application Insights pour le module complémentaire OSM AKS, consultez la page [Azure Monitor pour OSM](https://aka.ms/azmon/osmpreview).