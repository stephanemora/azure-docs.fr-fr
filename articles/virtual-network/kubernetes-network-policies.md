---
title: Stratégies réseau Azure Kubernetes | Microsoft Docs
description: Découvrez plus en détail les stratégies réseau Kubernetes pour sécuriser votre cluster Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159684"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Vue d’ensemble des stratégies réseau Azure Kubernetes

Les stratégies réseau permettent une micro-segmentation des pods tout comme les groupes de sécurité réseau (NSG) fournissent une micro-segmentation des machines virtuelles. L’implémentation d’une stratégie réseau Azure prend en charge la spécification d’une stratégie réseau Kubernetes standard. Vous pouvez utiliser des étiquettes pour sélectionner un groupe de pods et définir une liste de règles d’entrée et de sortie qui spécifient le type de trafic autorisé vers et à partir de ces pods. Découvrez plus en détail les stratégies réseau Kubernetes dans la [documentation Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Vue d’ensemble des stratégies réseau Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Les stratégies réseau Azure fonctionnent conjointement avec Azure CNI qui fournit l’intégration de réseau virtuel aux conteneurs. Il est actuellement pris en charge uniquement sur les nœuds Linux. Les implémentations configurent des règles de table d’adresses IP Linux basées sur les stratégies définies pour appliquer un filtrage du trafic.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planification de la sécurité pour votre cluster Kubernetes
Quand vous implémentez la sécurité de votre cluster, utilisez des groupes de sécurité réseau (NSG) pour filtrer le trafic nord-sud, c’est-à-dire le trafic entrant et sortant de votre sous-réseau de cluster, et utilisez des stratégies réseau Kubernetes pour le trafic est-ouest, c’est-à-dire le trafic entre les pods de votre cluster.

## <a name="using-azure-kubernetes-network-policies"></a>Utilisation des stratégies réseau Azure Kubernetes
Les stratégies réseau Azure peuvent être utilisées dans les méthodes suivantes pour fournir une micro-segmentation des pods.

### <a name="acs-engine"></a>ACS-engine
ACS-Engine est un outil qui génère un modèle Azure Resource Manager pour déployer un cluster Kubernetes dans Azure. La configuration du cluster est spécifiée dans un fichier JSON qui est passé à l’outil lors de la génération du modèle. Pour connaître la liste complète des paramètres de cluster pris en charge et leur description, consultez Moteur de Microsoft Azure Container Service - Définition du cluster.

Pour activer les stratégies sur des clusters déployés à l’aide d’acs-engine, définissez la valeur du paramètre networkPolicy dans le fichier de définition de cluster sur « azure ».

#### <a name="example-configuration"></a>Exemple de configuration

L’exemple de fichier JSON de configuration ci-dessous crée un réseau virtuel et un sous-réseau, et y déploie un cluster Kubernetes avec Azure CNI. Nous vous recommandons d’utiliser le « Bloc-notes » pour modifier le fichier JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Création de votre propre cluster Kubernetes dans Azure
L’implémentation peut être utilisée pour fournir des stratégies réseau aux pods dans des clusters Kubernetes que vous déployez vous-même, sans utiliser d’outils comme ACS-Engine. Dans ce cas, vous installez d’abord le plug-in CNI et l’activez sur chaque machine virtuelle d’un cluster. Pour obtenir des instructions détaillées, consultez [Déployer le plug-in pour un cluster Kubernetes que vous déployez vous-même](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Une fois le cluster déployé, exécutez la commande suivante `kubectl` pour télécharger et appliquer la stratégie réseau Azure *daemonset* au cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
La solution est également open source et le code est disponible dans le [dépôt Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Étapes suivantes
- Découvrez en détail [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Découvrez en détail la [mise en réseau de conteneurs](container-networking-overview.md).
- [Déployez le plug-in](deploy-container-networking.md) pour des clusters Kubernetes ou des conteneurs Docker.
