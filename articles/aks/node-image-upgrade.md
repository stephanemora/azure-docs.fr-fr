---
title: Mettre à niveau les images de nœud Azure Kubernetes service (AKS)
description: Découvrez comment mettre à niveau les images sur des nœuds de cluster AKS et des pools de nœuds.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 744e62f8a2207cff400a96069fc6ea82866f6e2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89055683"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Mise à niveau des images de nœud Azure Kubernetes service (AKS)

AKS prend en charge la mise à niveau des images sur un nœud afin que vous soyez à jour des dernières mises à jour du système d’exploitation et du runtime. AKS fournit une nouvelle image par semaine avec les dernières mises à jour. Il est donc préférable de régulièrement mettre à niveau les images de votre nœud pour bénéficier des dernières fonctionnalités, notamment les correctifs Linux ou Windows. Cet article explique comment mettre à niveau des images de nœud de cluster AKS et comment mettre à jour des images de pool de nœuds sans mettre à niveau la version de Kubernetes.

Si vous souhaitez en savoir plus sur les images les plus récentes fournies par AKS, consultez les [notes de publication AKS](https://github.com/Azure/AKS/releases) pour plus d’informations.

Pour plus d’informations sur la mise à niveau de la version Kubernetes pour votre cluster, consultez [Mettre à niveau un cluster AKS][upgrade-cluster].

## <a name="limitations"></a>Limites

* Le cluster AKS doit utiliser des groupes de machines virtuelles identiques pour les nœuds.

## <a name="install-the-aks-cli-extension"></a>Installer l’extension CLI AKS

Avant la publication de la version suivante de l’interface CLI principale, vous devez disposer de l’extension CLI *aks-preview* pour utiliser la mise à niveau des images de nœud. Utilisez la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Mettre à niveau tous les nœuds dans tous les pools de nœuds

La mise à niveau de l’image de nœud s’effectue avec `az aks upgrade`. Pour mettre à niveau l’image de nœud, utilisez la commande suivante :

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Pendant la mise à niveau, vérifiez l’état des images de nœud à l’aide de la commande `kubectl` suivante pour récupérer les étiquettes et filtrer les informations sur l’image de nœud actuelle :

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Une fois la mise à niveau terminée, utilisez `az aks show` pour obtenir les détails du pool de nœuds mis à jour. L’image de nœud actuelle est affichée dans la propriété `nodeImageVersion`.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Mettre à niveau un pool de nœuds spécifique

La mise à niveau de l’image sur un pool de nœuds est semblable à la mise à niveau de l’image sur un cluster.

Pour mettre à jour l’image du système d’exploitation du pool de nœuds sans effectuer de mise à niveau du cluster Kubernetes, utilisez l’option `--node-image-only` dans l’exemple suivant :

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Pendant la mise à niveau, vérifiez l’état des images de nœud à l’aide de la commande `kubectl` suivante pour récupérer les étiquettes et filtrer les informations sur l’image de nœud actuelle :

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Une fois la mise à niveau terminée, utilisez `az aks nodepool show` pour obtenir les détails du pool de nœuds mis à jour. L’image de nœud actuelle est affichée dans la propriété `nodeImageVersion`.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Mettre à niveau les images de nœud à l’aide de l’augmentation du nombre de nœuds

Pour accélérer le processus de mise à niveau des images de nœud, vous pouvez mettre à niveau vos images de nœud à l’aide d’une valeur personnalisable d’augmentation du nombre de nœuds. Par défaut, AKS utilise un nœud supplémentaire pour configurer les mises à niveau.

Si vous souhaitez augmenter la vitesse des mises à niveau, utilisez la valeur `--max-surge` pour configurer le nombre de nœuds à utiliser pour les mises à niveau afin qu’elles se terminent plus rapidement. Pour en savoir plus sur les compromis des différents paramètres `--max-surge`, consultez [Personnaliser la mise à niveau avec l’augmentation du nombre de nœuds][max-surge].

La commande suivante définit la valeur max-surge pour effectuer la mise à niveau d’une image de nœud :

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Pendant la mise à niveau, vérifiez l’état des images de nœud à l’aide de la commande `kubectl` suivante pour récupérer les étiquettes et filtrer les informations sur l’image de nœud actuelle :

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Utilisez `az aks nodepool show` pour récupérer les détails du pool de nœuds mis à jour. L’image de nœud actuelle est affichée dans la propriété `nodeImageVersion`.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les images de nœud les plus récentes, consultez les [notes de publication AKS](https://github.com/Azure/AKS/releases).
- Apprenez à mettre à niveau la version de Kubernetes avec la section [Mettre à niveau un cluster AKS][upgrade-cluster].
- [Appliquer des mises à jour de sécurité et du noyau à des nœuds Linux dans Azure Kubernetes Service (AKS)][security-update]
- En savoir plus sur les pools de nœuds multiples et sur la mise à niveau des pools de nœuds avec la section [Créer et gérer les pools de nœuds multiples][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
