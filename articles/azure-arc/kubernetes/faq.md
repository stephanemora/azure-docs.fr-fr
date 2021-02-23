---
title: Forum aux questions sur Kubernetes compatible avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article contient la liste des questions fréquemment posées relatives à Kubernetes compatible avec Azure Arc
keywords: Kubernetes, Arc, Azure, conteneurs, configuration, GitOps, FAQ
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561180"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Forum aux questions - Kubernetes compatible avec Azure Arc

Cet article traite des questions fréquemment posées sur Kubernetes compatible avec Azure Arc.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Quelle est la différence entre Azure Arc enabled Kubernetes et Azure Kubernetes Service (AKS) ?

AKS est l’offre de Kubernetes managé fournie par Azure. AKS simplifie le déploiement d’un cluster Kubernetes managé dans Azure en déchargeant une grande partie de la complexité et de la surcharge opérationnelle sur Azure. Sachant que les maîtres Kubernetes sont gérés par Azure, seules la gestion et la maintenance des nœuds agents vous incombent.

Kubernetes avec Azure Arc vous permet d’étendre les fonctionnalités de gestion d’Azure telles qu’Azure Monitor et Azure Policy en connectant des clusters Kubernetes à Azure. Vous conservez le cluster Kubernetes sous-jacent.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Dois-je connecter à Azure Arc mes clusters AKS qui s’exécutent sur Azure ?

Non. Toutes les fonctionnalités Kubernetes avec Azure Arc, y compris Azure Monitor et Azure Policy (Gatekeeper), sont disponibles sur AKS (une ressource native dans Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Dois-je connecter mon cluster AKS-HCI et les clusters Kubernetes sur Azure Stack Hub et Azure Stack Edge à Azure Arc ?

Oui, la connexion de votre cluster AKS-HCI ou de vos clusters Kubernetes sur Azure Stack Edge ou Azure Stack Hub à Azure Arc fournit des clusters avec une représentation de ressources dans Azure Resource Manager. Cette représentation des ressources étend des fonctionnalités telles que Cluster Configuration, Azure Monitor et Azure Policy (Gatekeeper) aux clusters Kubernetes que vous connectez.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Comment traiter des ressources Kubernetes avec Azure Arc ?

Le certificat d’identité MSI (Managed Service Identity) associé à votre instance Kubernetes compatible avec Azure Arc présente une fenêtre d’expiration de 90 jours. Une fois ce certificat expiré, la ressource est considérée `Expired` et toutes les fonctionnalités telles que la configuration, la surveillance et la stratégie cessent de fonctionner sur ce cluster. Procédez comme suit pour faire en sorte que votre cluster Kubernetes fonctionne à nouveau avec Azure Arc :

1. Supprimer la ressource et les agents Kubernetes compatible avec Azure Arc sur le cluster 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recréez la ressource Kubernetes compatible avec Azure Arc en redéployant des agents sur le cluster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` supprime également les configurations en plus du cluster. Après avoir exécuté `az connectedk8s connect`, recréez les configurations sur le cluster et ce, manuellement ou à l’aide d’Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Si j’utilise déjà des pipelines CI/CD, puis-je continuer à utiliser Kubernetes compatible avec Azure Arc et les configurations ?

Oui, vous pouvez toujours utiliser les configurations sur un cluster recevant des déploiements via un pipeline CI/CD. Comparées aux pipelines CI/CD classiques, les configurations offrent deux avantages supplémentaires :

**Rapprochement de dérive**

Le pipeline CI/CD n’applique les modifications qu’une seule fois pendant l’exécution du pipeline. Toutefois, l’opérateur GitOps du cluster interroge en permanence le référentiel Git pour extraire l’état souhaité des ressources Kubernetes sur le cluster. Si l’opérateur GitOps détermine que l’état souhaité des ressources est différent de l’état réel des ressources sur le cluster, cette dérive est rapprochée.

**Appliquer GitOps à grande échelle**

Les pipelines CI/CD sont adaptés aux déploiements pilotés par les événements sur votre cluster Kubernetes ; il peut notamment s’agir d’un envoi (push) vers un référentiel Git. Cela étant, le déploiement de la même configuration sur tous vos clusters Kubernetes requiert la configuration manuelle du pipeline CI/CD avec les informations d’identification de chacun de ces clusters Kubernetes. Pour autant, dans le cas de Kubernetes avec Azure Arc, Azure Resource Manager gèrant vos configurations, vous pouvez utiliser Azure Policy pour automatiser simultanément l’application de la configuration souhaitée à tous les clusters Kubernetes d’un abonnement ou d’une étendue de groupe de ressources. Cette fonctionnalité peut même s’appliquer aux ressources Kubernetes compatibles avec Azure Arc créées après l’attribution de la stratégie.

La fonctionnalité relative aux configurations est utilisée pour appliquer des configurations de base telles que des stratégies réseau, des liaisons de rôles et des stratégies de sécurité des pods à l’inventaire des clusters Kubernetes afin de répondre aux exigences de conformité et de gouvernance.

## <a name="next-steps"></a>Étapes suivantes

* [Connecter un cluster à Azure Arc](./connect-cluster.md)
* [Créer des configurations sur votre cluster Kubernetes compatible avec Azure Arc](./use-gitops-connected-cluster.md)
* [Utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md)
