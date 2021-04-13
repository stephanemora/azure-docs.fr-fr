---
title: Configurations et GitOps - Kubernetes compatible avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble de GitOps et des configurations de Kubernetes compatibles avec Azure Arc.
keywords: Kubernetes, Arc, Azure, conteneurs, configuration, GitOps
ms.openlocfilehash: 198bd45374d998ef0d9d0e612e974484d52e2f20
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443304"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configurations et GitOps avec Kubernetes compatible avec Azure Arc

Par rapport à Kubernetes, GitOps permet de déclarer l’état souhaité des configurations de cluster Kubernetes (déploiements, espaces de noms, etc.) dans un référentiel Git. Cette déclaration est suivie d’un déploiement d’interrogation et d’extraction de ces configurations de cluster à l’aide d’un opérateur. Le référentiel Git peut contenir les éléments suivants :
* Des manifestes au format YAML décrivant toutes sortes de ressources Kubernetes valides, telles que Namespaces, ConfigMaps, Deployments, DaemonSets, etc.
* Des graphiques Helm pour le déploiement d’applications.

[Flux](https://docs.fluxcd.io/), outil open source populaire dans l’espace GitOps, peut être déployé sur le cluster Kubernetes pour faciliter le flux des configurations entre un dépôt Git et un cluster Kubernetes. Flux prend en charge le déploiement de son opérateur sur les étendues de cluster et d’espace de noms. Un opérateur de flux déployé avec l’étendue d’espace de noms peut uniquement déployer des objets Kubernetes dans cet espace de noms spécifique. La possibilité de choisir entre l’étendue de cluster ou d’espace de noms vous permet d’obtenir des modèles de déploiement mutualisés sur le même cluster Kubernetes.

## <a name="configurations"></a>Configurations

[ ![Architecture des configurations](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

La connexion entre votre cluster et un dépôt Git est créée en tant que ressource de configuration (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) au-dessus de la ressource Kubernetes avec Azure Arc (représentée par `Microsoft.Kubernetes/connectedClusters`) dans Azure Resource Manager. 

Les propriétés des ressources de configuration permettent de déployer l’opérateur Flux sur le cluster avec les paramètres appropriés, par exemple le dépôt Git à partir duquel tirer les manifestes ainsi que la fréquence d’interrogation de leur extraction. Les données des ressources de configuration sont stockées et chiffrées au repos dans une base de données Azure Cosmos DB pour garantir leur confidentialité.

Le `config-agent` en cours d’exécution dans votre cluster est responsable des tâches suivantes :
* Suivi des ressources de configuration nouvelles ou mises à jour sur la ressource Kubernetes avec Azure Arc.
* Déploiement d’un opérateur Flux pour surveiller chaque ressource de configuration du dépôt Git.
* Application des mises à jour apportées aux ressources de configuration. 

Vous pouvez créer plusieurs ressources de configuration ayant pour étendue un espace de noms sur le même cluster Kubernetes avec Azure Arc pour obtenir une architecture multilocataire.

> [!NOTE]
> * `config-agent` supervise la disponibilité des ressources de configuration nouvelles ou mises à jour sur la ressource Kubernetes avec Arc. Les agents ont donc besoin d’une connectivité pour que l’état souhaité soit extrait vers le cluster. Si les agents ne parviennent pas à se connecter à Azure, il se produit un retard dans la propagation de l’état souhaité vers le cluster.
> * Les entrées de clients sensibles telles que la clé privée, le contenu des hôtes connus, le nom d’utilisateur HTTPS et le jeton/mot de passe ne sont pas stockées au-delà de 48 heures dans les services Kubernetes compatibles avec Azure Arc. Si vous utilisez des entrées sensibles pour les configurations, mettez les clusters en ligne aussi régulièrement que possible.

## <a name="apply-configurations-at-scale"></a>Appliquer des configurations à grande échelle

Dans la mesure où Azure Resource Manager gère vos configurations, vous pouvez automatiser la création d’une configuration identique sur toutes les ressources Kubernetes avec Azure Arc via Azure Policy, dans l’étendue d’un abonnement ou d’un groupe de ressources. 

À travers cette mise en œuvre à grande échelle, vous avez la garantie qu’une configuration de base de référence commune (contenant des configurations telles que ClusterRoleBindings, RoleBindings et NetworkPolicy) peut être appliquée à l’ensemble d’une flotte ou d’un inventaire de clusters Kubernetes avec Azure Arc.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Créez des configurations sur votre cluster Kubernetes avec Azure Arc](./tutorial-use-gitops-connected-cluster.md).
* [Utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md).