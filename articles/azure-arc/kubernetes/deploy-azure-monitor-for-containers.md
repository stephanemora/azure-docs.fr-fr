---
title: Intégrer Azure Arc avec Azure Monitor pour des conteneurs (préversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Intégrer Azure Arc avec Azure Monitor pour des conteneurs
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680736"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Intégrer Azure Monitor pour conteneurs avec Arc (préversion)

Intégration des [conteneurs compatibles avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) à des clusters Kubernetes compatibles avec Azure Arc.

## <a name="before-you-begin"></a>Avant de commencer

* [Versions de Kubernetes](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Distributions Linux pour les nœuds de cluster (master & worker) – Ubuntu (18.04 LTS et 16.04 LTS)
* Autorisation minimale du rôle RBAC de Contributeur sur l’abonnement Azure du cluster Kubernetes compatible avec Azure Arc activé
* ID de ressource Azure complet du cluster Kubernetes compatible avec Azure Arc
* Contexte Kubeconfig du cluster Kubernetes
* L’agent de surveillance requiert que cAdvisor sur le Kubelet s’exécute sur un port sécurisé : port 10250 ou non sécurisé : port 10255 sur tous les nœuds pour extraire les métriques de performances   
* Il est recommandé de configurer le port Kubelet cAdvisor pour sécuriser le port : 10250.
* L’agent de surveillance requiert les ports de sortie et les domaines suivants pour envoyer les données de supervision au serveur principal Azure Monitor (s’il est bloqué par un proxy/pare-feu)
    -  *.ods.opinsights.azure.com 443
    -  *.oms.opinsights.azure.com 443
    -  *.blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Mise en route

### <a name="using-helm-chart"></a>Utilisation du graphique HELM

### <a name="option-1-using-powershell--script"></a>Option 1 : Utilisation du script PowerShell

1. Télécharger le script d’intégration

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Installez [PowerShell Core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) sur votre ordinateur de développement pour exécuter le script d’intégration PowerShell.

3. Connexion à Azure

    ```console
    az login --use-device-code
    ```

4. Exécutez le script ci-dessous avec votre cluster Azure Arc K8s Cluster ResourceId et le contexte du cluster Kubernetes

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Option n°2 : Utilisation de script Bash

> **Conseil :** Le script utilise des fonctionnalités bash 4. Assurez-vous que votre version de bash est à jour. Vous pouvez vérifier votre version actuelle avec `bash --version`.

1. Télécharger le script d’intégration

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Exécutez le script ci-dessous avec votre cluster Azure Arc K8s Cluster ResourceId et le contexte du cluster Kubernetes

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Configurer la collecte de données de l’agent

Par défaut, l’agent ne collecte pas les journaux stdout et stderr des conteneurs dans l’espace de noms kube-system.
Reportez-vous à https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config pour configurer l’agent avec les paramètres de collecte de données souhaités.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurer la capture des métriques de Prometheus

Azure Monitor pour conteneurs capture les métriques de Prometheus et les ingestions sur le back-end Azure Monitor.
Reportez-vous à https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration pour obtenir des instructions sur la configuration du scraping Prometheus.

## <a name="user-interface"></a>Interface utilisateur

Accédez à https://aka.ms/azmon-containers-azurearc pour afficher le cluster intégré.

## <a name="disable-monitoring"></a>Désactiver l’analyse

Si vous souhaitez désactiver la supervision, vous pouvez simplement supprimer le diagramme HELM Azure Monitor pour des conteneurs afin d’arrêter la collecte et l’ingestion des données de supervision sur un serveur principal Azure Monitor pour conteneurs.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser Azure Policy pour gérer la configuration du cluster](./use-azure-policy.md)

