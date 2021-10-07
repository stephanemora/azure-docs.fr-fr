---
title: Activer le Gestionnaire de contrôleurs cloud (préversion)
description: Découvrir comment activer le fournisseur de cloud externe (out-of-tree)
services: container-service
ms.topic: article
ms.date: 8/25/2021
ms.author: juda
ms.openlocfilehash: 98f8fe2e00a7671078da7dae2174401b6c33fd30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667613"
---
# <a name="enable-cloud-controller-manager-preview"></a>Activer le Gestionnaire de contrôleurs cloud (préversion)

En tant que fournisseur de cloud, Microsoft Azure travaille en étroite collaboration avec la communauté Kubernetes pour prendre en charge notre infrastructure pour le compte d’utilisateurs.

Actuellement, l’intégration d’un fournisseur de cloud dans Kubernetes s’effectue en interne (« in-tree »), où tous les changements apportés aux fonctionnalités spécifiques au cloud doivent suivre le cycle de publication Kubernetes standard.  Quand nous détectons des problèmes, que nous en corrigeons ou que nous devons déployer des améliorations, nous devons le faire dans le cycle de publication de la communauté Kubernetes.

La communauté Kubernetes adopte désormais un modèle « en externe» (out-of-tree), où les fournisseurs de cloud contrôlent leurs versions indépendamment du calendrier de publication Kubernetes principal par le biais du composant [cloud-provider-azure][cloud-provider-azure].  Nous avons déjà déployé les pilotes CSI (Cloud Stockage Interface) comme pilotes par défaut dans Kubernetes version 1.21 et versions ultérieures.

> [!Note]
> Le fait d’activer le Gestionnaire de contrôleurs cloud sur votre cluster AKS active également les pilotes CSI externes (out-of-tree).

Le Gestionnaire de contrôleurs cloud sera le contrôleur par défaut de Kubernetes 1.22, pris en charge par AKS.


[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Avant de commencer

La ressource suivante doit être installée :

* L’interface Azure CLI
* L’extension `aks-preview` version 0.5.5 ou ultérieure
* Kubernetes version 1.20.x ou ultérieure


### <a name="register-the-enablecloudcontrollermanager-feature-flag"></a>Inscrire l’indicateur de fonctionnalité `EnableCloudControllerManager`

Pour utiliser les fonctionnalité du Gestionnaire de contrôleurs cloud, vous devez activer l’indicateur de fonctionnalité `EnableCloudControllerManager` sur votre abonnement. 

```azurecli
az feature register --name EnableCloudControllerManager --namespace Microsoft.ContainerService
```
Vous pouvez vérifier l’état de l’enregistrement à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableCloudControllerManager')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-cloud-controller-manager"></a>Créer un cluster AKS avec le Gestionnaire de contrôleurs cloud

Pour créer un cluster avec le Gestionnaire de contrôleurs cloud, passez `EnableCloudControllerManager=True` comme en-tête client à l’API Azure à l’aide d’Azure CLI.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="upgrade-an-aks-cluster-to-cloud-controller-manager"></a>Mettre à niveau un cluster AKS vers le Gestionnaire de contrôleurs cloud

Pour mettre à niveau un cluster afin d’utiliser le Gestionnaire de contrôleurs cloud, passez `EnableCloudControllerManager=True` comme en-tête client à l’API Azure à l’aide d’Azure CLI.

```azurecli-interactive
az aks upgrade -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les pilotes CSI et le comportement par défaut pour les versions de Kubernetes ultérieures à la version 1.21, consultez notre [documentation][csi-docs].

- Des informations supplémentaires sur la direction de la communauté Kubernetes concernant les fournisseurs externes (out-of-tree) sur le [billet de blog de la communauté][community-blog].


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[csi-docs]: csi-storage-drivers.md

<!-- LINKS - External -->
[community-blog]: https://kubernetes.io/blog/2019/04/17/the-future-of-cloud-providers-in-kubernetes
[cloud-provider-azure]: https://github.com/kubernetes-sigs/cloud-provider-azure
