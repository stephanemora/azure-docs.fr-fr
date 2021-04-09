---
title: Déploiement et métriques HPA avec Container Insights | Microsoft Docs
description: Cet article décrit les métriques de déploiement et HPA (autoscaler de pods élastique) collectées avec Container Insights.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717704"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Déploiement et métriques HPA avec Container Insights

À partir de la version de l’agent *ciprod08072020*, l’agent intégré de Container Insights collecte désormais les métriques pour les déploiements et HPA.

## <a name="deployment-metrics"></a>Métriques de déploiement

Container Insights lance automatiquement la supervision des déploiements en collectant les métriques suivantes toutes les 60 secondes et en les stockant dans la table **InsightMetrics** :

|Nom de métrique |Dimension de la métrique (balises) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status.updatedReplicas) | Nombre total de pods prêts ciblés par ce déploiement (status.readyReplicas). Vous trouverez ci-dessous les dimensions de cette métrique. <ul> <li> deployment : nom du déploiement </li> <li> k8sNamespace : l’espace de noms Kubernetes pour le déploiement </li> <li> deploymentStrategy : la stratégie de déploiement à utiliser pour remplacer les pods par de nouveaux (spec.strategy.type)</li><li> creationTime : horodatage de création du déploiement </li> <li> spec_replicas : nombre de pods souhaités (spec.replicas) </li> <li>status_replicas_available : nombre total de pods disponibles (prêts pour au moins minReadySeconds) ciblés par ce déploiement (status.availableReplicas)</li><li>status_replicas_updated : nombre total de pods non terminés ciblés par ce déploiement et ayant les spécifications de modèle souhaitées (status.updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>Métriques HPA

Container Insights lance automatiquement la supervision des HPA en collectant les métriques suivantes toutes les 60 secondes et en les stockant dans la table **InsightMetrics** :

|Nom de métrique |Dimension de la métrique (balises) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hpa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | Nombre actuel de réplicas de pods gérés par cette mise à l’échelle automatique (status.currentReplicas). Vous trouverez ci-dessous les dimensions de cette métrique. <ul> <li> hpa : nom du HPA </li> <li> k8sNamespace : l’espace de noms Kubernetes pour le HPA </li> <li> lastScaleTime : dernière fois que le HPA a mis à l’échelle le nombre de pods (status.lastScaleTime)</li><li> creationTime : horodateur de création du HPA </li> <li> spec_max_replicas : limite supérieure pour le nombre de pods pouvant être définis par la mise à l’échelle automatique (spec.maxReplicas) </li> <li> spec_min_replicas : limite inférieure pour le nombre de réplicas jusqu’auquel la mise à l’échelle automatique peut monter en puissance (spec.minReplicas) </li><li>status_desired_replicas : nombre souhaité de réplicas de pods gérés par cette mise à l’échelle automatique (status.desiredReplicas)</li><li>targetKind : type de la cible du HPA (spec.scaleTargetRef.kind) </li><li>targetName : nom de la cible du HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Graphiques de déploiement & HPA 

Container Insights inclut des graphiques préconfigurés pour les métriques listées dans le tableau précédent sous la forme d’un classeur pour chaque cluster. Vous pouvez trouver le classeur de déploiements et HPA **Déploiements et HPA** directement à partir d’un cluster AKS en sélectionnant **Classeurs** dans le volet de gauche, puis à partir de la liste déroulante **Voir les classeurs** dans Insight.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les métriques d’état de Kube, consultez [Métriques d’état de Kube dans Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs).