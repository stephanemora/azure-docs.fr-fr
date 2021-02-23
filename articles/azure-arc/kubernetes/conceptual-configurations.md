---
title: Configurations et GitOps - Kubernetes compatible avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble de GitOps et des configurations de Kubernetes compatibles avec Azure Arc.
keywords: Kubernetes, Arc, Azure, conteneurs, configuration, GitOps
ms.openlocfilehash: 780c3c5c578c8a9b12eb7dda711070790477ac5f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561145"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configurations et GitOps avec Kubernetes compatible avec Azure Arc

Par rapport à Kubernetes, GitOps permet de déclarer l’état souhaité des configurations de cluster Kubernetes (déploiements, espaces de noms, etc.) dans un référentiel Git. Cette déclaration est suivie d’un déploiement d’interrogation et d’extraction de ces configurations de cluster à l’aide d’un opérateur. Le référentiel Git peut contenir les éléments suivants :
* Des manifestes au format YAML décrivant toutes sortes de ressources Kubernetes valides, telles que Namespaces, ConfigMaps, Deployments, DaemonSets, etc.
* Des graphiques Helm pour le déploiement d’applications.

[Flux](https://docs.fluxcd.io/), outil open source populaire de l’espace GitOps, peut être déployé sur le cluster Kubernetes pour faciliter le flux des configurations entre un référentiel Git et un cluster Kubernetes. Flux prend en charge le déploiement de son opérateur sur les étendues de cluster et d’espace de noms. Un opérateur de flux déployé avec l’étendue d’espace de noms peut uniquement déployer des objets Kubernetes dans cet espace de noms spécifique. La possibilité de choisir entre l’étendue de cluster ou d’espace de noms vous permet d’obtenir des modèles de déploiement mutualisés sur le même cluster Kubernetes.

## <a name="configurations"></a>Configurations

[ ![Architecture des configurations](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

La connexion entre votre cluster et un référentiel Git est créée en tant que ressource d’extension `Microsoft.KubernetesConfiguration/sourceControlConfigurations` en plus de la ressource Kubernetes compatible avec Azure Arc (représentée par `Microsoft.Kubernetes/connectedClusters`) dans Azure Resource Manager. 

Les propriétés de la ressource `sourceControlConfiguration` sont utilisées pour déployer l’opérateur de flux sur le cluster avec les paramètres appropriés, tels que le référentiel Git à partir duquel extraire les manifestes et la fréquence d’interrogation à laquelle les extraire. Les données `sourceControlConfiguration` sont stockées et chiffrées au repos dans une base de données Azure Cosmos DB pour garantir leur confidentialité.

Le `config-agent` en cours d’exécution dans votre cluster est responsable des tâches suivantes :
* Suivi des ressources d’extension `sourceControlConfiguration`, nouvelles ou mises à jour, sur la ressource Kubernetes compatible avec Azure Arc.
* Déploiement d’un opérateur de flux afin d’observer le référentiel Git pour chaque `sourceControlConfiguration`.
* Application des mises à jour apportées à ni’mporte quel `sourceControlConfiguration` . 

Vous pouvez créer plusieurs ressources avec étendue d’espace de noms `sourceControlConfiguration` sur le même cluster Kubernetes compatible avec Azure Arc afin d’obtenir une architecture mutualisée.

> [!NOTE]
> * `config-agent` surveillant la disponibilité des ressources d’extension `sourceControlConfiguration`, nouvelles ou mises à jour, sur la ressource Kubernetes compatible avec Azure Arc, les agents doivent disposer d’une connectivité pour que l’état souhaité soit extrait vers le cluster. Lorsque les agents ne sont pas en mesure de se connecter à Azure, les propriétés de l’état souhaité déclarées sur la ressource `sourceControlConfiguration` dans Azure Resource Manager ne sont pas appliquées au cluster.
> * Les entrées de clients sensibles telles que la clé privée, le contenu des hôtes connus, le nom d’utilisateur HTTPS et le jeton/mot de passe ne sont pas stockées au-delà de 48 heures dans les services Kubernetes compatibles avec Azure Arc. Si vous utilisez des entrées sensibles pour les configurations, il vous est recommandé de mettre les clusters en ligne aussi régulièrement que possible.

## <a name="apply-configurations-at-scale"></a>Appliquer des configurations à grande échelle

Azure Resource Manager gérant vos configurations, vous pouvez utiliser Azure Policy pour automatiser la création de la même configuration sur toutes les ressources Kubernetes compatibles avec Azure Arc dans l’étendue d’un abonnement ou d’un groupe de ressources. 

Cette application à grande échelle veille à ce qu’une configuration de ligne de base commune (contenant des configurations telles que ClusterRoleBindings, RoleBindings et NetworkPolicy) puisse être appliquée à l’ensemble de la flotte ou de l’inventaire des clusters Kubernetes compatibles avec Azure Arc.

## <a name="next-steps"></a>Étapes suivantes

* [Connecter un cluster à Azure Arc](./connect-cluster.md)
* [Créer des configurations sur votre cluster Kubernetes compatible avec Azure Arc](./use-gitops-connected-cluster.md)
* [Utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md)