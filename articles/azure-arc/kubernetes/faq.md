---
title: Forum aux questions sur Kubernetes compatible avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article contient la liste des questions fréquemment posées relatives à Kubernetes compatible avec Azure Arc
keywords: Kubernetes, Arc, Azure, conteneurs, configuration, GitOps, FAQ
ms.openlocfilehash: f678bd23bc4e9e40f718d72ccde8069c36673ac6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272922"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Forum aux questions - Kubernetes compatible avec Azure Arc

Cet article traite des questions fréquemment posées sur Kubernetes compatible avec Azure Arc.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Quelle est la différence entre Azure Arc enabled Kubernetes et Azure Kubernetes Service (AKS) ?

AKS est l’offre de Kubernetes managé fournie par Azure. AKS simplifie le déploiement d’un cluster Kubernetes managé dans Azure en déchargeant une grande partie de la complexité et de la surcharge opérationnelle sur Azure. Sachant que les maîtres Kubernetes sont gérés par Azure, seules la gestion et la maintenance des nœuds agents vous incombent.

Kubernetes avec Azure Arc vous permet d’étendre les fonctionnalités de gestion d’Azure telles qu’Azure Monitor et Azure Policy en connectant des clusters Kubernetes à Azure. Vous conservez le cluster Kubernetes sous-jacent.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Dois-je connecter à Azure Arc mes clusters AKS qui s’exécutent sur Azure ?

La connexion d’un cluster Azure Kubernetes Service (AKS) à Azure Arc n’est requise que pour l’exécution de services avec Arc, comme App Services et Data Services, sur le cluster. Pour ce faire, vous pouvez utiliser la fonctionnalité d’[emplacements personnalisés](custom-locations.md) de Kubernetes avec Arc. Il s’agit pour l’instant d’une limite dans le temps jusqu’à ce que les extensions de cluster et les emplacements personnalisés soient introduits en mode natif sur les clusters AKS.

Si vous ne souhaitez pas utiliser d’emplacements personnalisés mais simplement des fonctionnalités de gestion telles que Azure Monitor et Azure Policy (Gatekeeper), celles-ci sont disponibles en mode natif sur AKS sans qu’aucune connexion à Azure Arc ne soit requise.
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Dois-je connecter mon cluster AKS-HCI et les clusters Kubernetes sur Azure Stack Hub et Azure Stack Edge à Azure Arc ?

Oui, la connexion de votre cluster AKS-HCI ou de vos clusters Kubernetes sur Azure Stack Edge ou Azure Stack Hub à Azure Arc fournit des clusters avec une représentation de ressources dans Azure Resource Manager. Cette représentation des ressources étend des fonctionnalités telles que la configuration de cluster, Azure Monitor et Azure Policy (Gatekeeper) aux clusters Kubernetes connectés.

Si le cluster Kubernetes avec Azure Arc est sur Azure Stack Edge, AKS sur Azure Stack HCI (>= mise à jour d’avril 2021) ou AKS sur Windows Server 2019 Datacenter (>= mise à jour d’avril 2021), la configuration de Kubernetes est incluse gratuitement.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Comment traiter des ressources Kubernetes avec Azure Arc ?

L’identité managée attribuée par le système associée à votre cluster Kubernetes avec Azure Arc est utilisée uniquement par les agents Arc pour communiquer avec les services Azure Arc. Le certificat que le système associe à cette identité managée a une fenêtre d’expiration de 90 jours, et les agents continuent de tenter de le renouveler entre les 46e et 90e jours. Une fois ce certificat expiré, la ressource est considérée comme `Expired`, et toutes les fonctionnalités (telles que la configuration, la surveillance et la stratégie) cessent de fonctionner sur ce cluster. Vous devez alors supprimer le cluster et le reconnecter à Azure Arc. Il est donc recommandé de faire en sorte que le cluster revienne en ligne au moins une fois entre les 46e et 90e jours de la fenêtre de temps pour garantir le renouvellement du certificat d’identité managée.

Pour vérifier si le certificat est sur le point d’expirer pour un cluster donné, exécutez la commande suivante :

```console
az connectedk8s show -n <name> -g <resource-group>
```

Dans la sortie, la valeur `managedIdentityCertificateExpirationTime` indique la date d’expiration du certificat d’identité managée (marque 90D Mark pour ce certificat). 

Si la valeur `managedIdentityCertificateExpirationTime` indique un horodatage (timestamp) passé, le champ `connectivityStatus` dans la sortie ci-dessus est défini sur `Expired`. Dans de tels cas, pour que votre cluster Kubernetes fonctionne à nouveau avec Azure Arc :

1. Supprimez la ressource et les agents Kubernetes avec Azure Arc sur le cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recréez la ressource Kubernetes avec Azure Arc en déployant des agents sur le cluster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` va également supprimer les configurations et les extensions de cluster sur le cluster. Après avoir exécuté `az connectedk8s connect`, recréez les configurations et les extensions de cluster sur le cluster, manuellement ou à l’aide d’Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Si j’utilise déjà des pipelines CI/CD, puis-je continuer à utiliser Kubernetes compatible avec Azure Arc et les configurations ?

Oui, vous pouvez toujours utiliser les configurations sur un cluster recevant des déploiements via un pipeline CI/CD. Comparées aux pipelines CI/CD classiques, les configurations offrent deux avantages supplémentaires :

**Rapprochement de dérive**

Le pipeline CI/CD n’applique les modifications qu’une seule fois pendant l’exécution du pipeline. Toutefois, l’opérateur GitOps du cluster interroge en permanence le référentiel Git pour extraire l’état souhaité des ressources Kubernetes sur le cluster. Si l’opérateur GitOps détermine que l’état souhaité des ressources est différent de l’état réel des ressources sur le cluster, cette dérive est rapprochée.

**Appliquer GitOps à grande échelle**

Les pipelines CI/CD sont utiles pour les déploiements basés sur les événements sur votre cluster Kubernetes (par exemple un envoi (push) vers un dépôt Git). Toutefois, si vous souhaitez déployer la même configuration sur tous vos clusters Kubernetes, vous devez configurer manuellement les informations d’identification de chaque cluster Kubernetes sur le pipeline CI/CD. 

Pour Kubernetes avec Azure Arc, dans la mesure où Azure Resource Manager gère vos configurations, vous pouvez automatiser la création d’une configuration identique sur toutes les ressources Kubernetes avec Azure Arc via Azure Policy, dans l’étendue d’un abonnement ou d’un groupe de ressources. Cette fonctionnalité peut même s’appliquer aux ressources Kubernetes compatibles avec Azure Arc créées après l’attribution de la stratégie.

Cette fonctionnalité applique des configurations de base de référence (par exemple des stratégies réseau, des liaisons de rôles et des stratégies de sécurité des pods) à l’ensemble de l’inventaire de clusters Kubernetes pour répondre aux impératifs de conformité et de gouvernance.

## <a name="next-steps"></a>Étapes suivantes

* Parcourez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* Un cluster Kubernetes est-il déjà connecté à Azure Arc ? [Créez des configurations sur votre cluster Kubernetes compatible avec Arc](./tutorial-use-gitops-connected-cluster.md).
* Découvrez comment [utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md).