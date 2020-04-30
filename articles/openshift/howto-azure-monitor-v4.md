---
title: Intégration d’Azure Monitor pour Azure Red Hat OpenShift 4.3
description: Découvrez comment activer Azure Monitor sur votre cluster Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082844"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Intégration d’Azure Monitor pour Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Notez qu’Azure Red Hat OpenShift 4.3 est disponible uniquement en préversion privée dans la région USA Est. L’acceptation de la préversion privée se fait sur invitation uniquement. Inscrivez votre abonnement avant d’essayer d’activer cette fonctionnalité : [Inscription à la préversion privée d’Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Les fonctionnalités d’évaluation sont en libre-service et sont fournies en l’état et en fonction des disponibilités. De plus, elles sont exclues du contrat de niveau de service (SLA) et de la garantie limitée. Par conséquent, ces fonctionnalités ne sont pas destinées à une utilisation en production.

Cet article explique comment activer la préversion privée d’Azure Monitor pour conteneurs pour les clusters OpenShift 4.3 hébergés localement ou dans n’importe quel environnement cloud. Les mêmes instructions s’appliquent également pour activer la supervision des clusters Azure Red Hat OpenShift (ARO) 4.3.  

## <a name="prerequisites"></a>Prérequis

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Accès aux données kubeconfig du cluster kubernetes
- l’accès à un abonnement Azure ;
- Accès au cluster OpenShift 4.3 afin d’installer le chart Helm Azure Monitor pour conteneurs
- Autorisation minimale du rôle RBAC Contributeur sur l’abonnement Azure  
- L’agent de surveillance requiert les ports de sortie et les domaines suivants pour envoyer les données de supervision au back-end Azure Monitor (s’il est bloqué par le proxy/pare-feu) :
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Mise en route

> [!TIP]
> Le script utilise des fonctionnalités bash 4. Assurez-vous que votre version de bash est à jour. Vous pouvez vérifier votre version actuelle avec `bash --version`.

### <a name="download-the-onboarding-script"></a>Télécharger le script d’intégration

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Exécutez le script suivant en indiquant l’ID d’inscription Azure (azureSubscriptionId), la région de l’espace de travail, le nom de cluster (clusterName) et le contexte du cluster Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Par exemple :

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Configurer la collecte de données de l’agent

Par défaut, l’agent de supervision collecte les journaux de conteneurs {stdout ; stderr} de tous les conteneurs en cours d’exécution dans tous les espaces de noms, à l’exception de kube-system.  Si vous souhaitez configurer la collection de journaux de conteneurs propre à un espace de noms ou à des espaces de noms particuliers, vous pouvez vous reporter à [Configuration de l’agent Container Insights](../azure-monitor/insights/container-insights-agent-config.md). Là, vous pouvez configurer l’agent de supervision avec les paramètres de collecte de données souhaités à l’aide du mappage de configuration.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurer la capture des métriques de Prometheus

Azure Monitor pour conteneurs capture les métriques de Prometheus et les ingestions sur le back-end Azure Monitor. Pour obtenir des instructions sur la configuration de la capture des métriques de Prometheus, reportez-vous à [Configuration de Prometheus pour Container Insights](../azure-monitor/insights/container-insights-prometheus-integration.md).

Une fois l’intégration réussie, accédez à [Supervision hybride](https://aka.ms/azmon-containers-hybrid) et sélectionnez **Tout** pour Environnement afin d’afficher le cluster OpenShift v4 récemment intégré.

## <a name="disable-monitoring"></a>Désactiver l’analyse

Si vous souhaitez désactiver la supervision, vous pouvez supprimer le chart Helm Azure Monitor pour conteneurs à l’aide de la commande suivante pour arrêter la collecte et l’ingestion des données de supervision sur le back-end Azure Monitor pour conteneurs.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Mettre à jour la supervision

Réexécutez le script d’intégration comme décrit dans la section [Intégration](#onboarding) avec le même paramètre pour effectuer une mise à jour vers le chart Helm le plus récent.

## <a name="after-successful-onboarding"></a>Après la réussite de l’intégration

Accédez à [Supervision hybride](https://aka.ms/azmon-containers-hybrid). Vous pouvez voir votre nouveau cluster activé OpenShift/ARO v4 avec l’état d’intégrité sous l’onglet **Clusters supervisés**. À cet endroit, vous pouvez accéder à des insights plus détaillés, tels que les métriques, l’inventaire et les journaux, en cliquant sur la colonne **Cluster**.

## <a name="supported-features"></a>Fonctionnalités prises en charge

Pour plus d’informations sur les fonctionnalités prises en charge, consultez [Vue d’ensemble de Container Insights](../azure-monitor/insights/container-insights-overview.md).

Contactez-nous par le biais de l’adresse askcoin@microsoft.com pour formuler des commentaires et des questions.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la supervision, consultez :
- [Vue d’ensemble de Container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Vue d’ensemble des requêtes de journal](../azure-monitor/log-query/log-query-overview.md)
