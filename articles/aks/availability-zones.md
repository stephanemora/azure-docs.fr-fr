---
title: Utiliser des zones de disponibilité dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer un cluster qui distribue les nœuds entre les zones de disponibilité dans Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596808"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Créer un cluster Azure Kubernetes Service (AKS) qui utilise des zones de disponibilité

Un cluster AKS (Azure Kubernetes Service) répartit les ressources telles que les nœuds et le stockage entre les sections logiques de l'infrastructure de calcul Azure sous-jacente. Ce modèle de déploiement permet de s'assurer que les nœuds s'exécutent sur des domaines de mise à jour et d’erreur distincts d’un même centre de données Azure. Les clusters AKS déployés avec ce comportement par défaut offrent un niveau élevé de disponibilité pour se protéger contre une panne matérielle ou un événement de maintenance planifiée.

Pour offrir un niveau de disponibilité plus élevé à vos applications, les clusters AKS peuvent être répartis sur plusieurs zones de disponibilité. Ces zones représentent des centres de données physiquement séparés au sein d’une région donnée. Lorsque les composants du cluster sont répartis sur plusieurs zones, votre cluster AKS est capable de tolérer une défaillance dans l'une de ces zones. Vos applications et vos opérations de gestion restent disponibles même si un centre de données complet rencontre un problème.

Cet article vous explique comment créer un cluster AKS et répartir les composants de nœud entre des zones de disponibilité.

## <a name="before-you-begin"></a>Avant de commencer

La version 2.0.76 d’Azure CLI (ou ultérieure) doit être installée et configurée. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitations et disponibilité de la région

Les clusters AKS peuvent actuellement être créés en utilisant des zones de disponibilité dans les régions suivantes :

* USA Centre
* USA Est 2
* USA Est
* France Centre
* Japon Est
* Europe Nord
* Asie Sud-Est
* Sud du Royaume-Uni
* Europe Ouest
* USA Ouest 2

Les limitations suivantes s'appliquent lorsque vous créez un cluster AKS à l'aide de zones de disponibilité :

* Vous ne pouvez activer les zones de disponibilité que lorsque le cluster est créé.
* Les paramètres de la zone de disponibilité ne peuvent pas être mis à jour une fois le cluster créé. Vous ne pouvez pas non plus mettre à jour un cluster de zones de non-disponibilité existant pour utiliser des zones de disponibilité.
* Vous ne pouvez pas désactiver les zones de disponibilité d’un cluster AKS une fois qu'il a été créé.
* La taille de nœud (référence SKU de la machine virtuelle) sélectionnée doit être disponible dans toutes les zones de disponibilité.
* Les clusters avec des zones de disponibilité activées nécessitent l'utilisation d'équilibreurs de charge Standard Azure pour la distribution entre les zones.
* Vous devez utiliser Kubernetes version 1.13.5 ou supérieure pour déployer des équilibreurs de charge Standard.

Les clusters AKS qui emploient des zones de disponibilité doivent utiliser la référence SKU *Standard* de l’équilibreur de charge Azure, qui est la valeur par défaut pour le type équilibreur de charge. Ce type d’équilibreur de charge ne peut être défini qu’au moment de la création du cluster. Pour plus d'informations et connaître les limites de l’équilibreur de charge Standard, consultez [Limites de la référence SKU Standard de l’équilibreur de charge Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitations des disques Azure

Les volumes qui utilisent des disques managés Azure ne constituent pas actuellement des ressources zonales. Les pods replanifiés dans une zone différente de leur zone d'origine ne peuvent pas reconnecter leur(s) disque(s) précédent(s). Il est recommandé d'exécuter des charges de travail sans état qui ne nécessitent pas de stockage persistant pouvant présenter des problèmes zonaux.

Si vous devez exécuter des charges de travail sans état, utilisez les teintes et les tolérances des spécifications de vos pods pour indiquer au planificateur Kubernetes de créer des pods dans la même zone que vos disques. Vous pouvez également utiliser un stockage en réseau, par exemple Azure Files, pour vous connecter aux pods à mesure qu'ils sont planifiés entre les zones.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Vue d’ensemble des zones de disponibilité pour les clusters AKS

Les zones de disponibilité sont une offre à haute disponibilité qui protège vos applications et vos données contre les défaillances des centres de données. Les zones sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, il existe un minimum de trois zones distinctes dans toutes les régions activées. La séparation physique des zones de disponibilité dans une région protège les applications et les données des défaillances dans le centre de données. Les services redondants interzone répliquent vos applications et vos données entre des zones de disponibilité pour les protéger contre des points de défaillance uniques.

Pour plus d’informations, consultez [Que sont les zones de disponibilité dans Azure ?][az-overview].

Les clusters AKS déployés à l'aide de zones de disponibilité peuvent répartir les nœuds sur plusieurs zones au sein d'une même région. Par exemple, un cluster dans la région  *USA Est 2*  peut créer des nœuds dans les trois zones de disponibilité de *USA Est 2*. Cette distribution des ressources du cluster AKS améliore la disponibilité du cluster car elles résistent aux défaillances d'une zone spécifique.

![Distribution des nœuds AKS entre les zones de disponibilité](media/availability-zones/aks-availability-zones.png)

En cas de défaillance d’une zone, les nœuds peuvent être rééquilibrés manuellement ou à l'aide de l'autochargeur de cluster. Si une seule zone devient indisponible, vos applications continuent de s'exécuter.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Créer un cluster AKS sur plusieurs zones de disponibilité

Lorsque vous créez un cluster à l'aide de la commande [az aks create][az-aks-create], le paramètre `--zones` définit dans quelles zones les nœuds d'agent sont déployés. Les composants du plan de contrôle AKS de votre cluster sont également répartis entre les zones, dans la configuration la plus disponible qui soit lorsque vous définissez le paramètre `--zones` au moment de la création du cluster.

Si vous ne définissez aucune zone pour le pool d'agents par défaut lorsque vous créez un cluster AKS, les composants du plan de contrôle AKS de votre cluster n'utiliseront aucune zone de disponibilité. Vous pouvez ajouter des pools de nœuds supplémentaires en utilisant la commande [az aks nodepool add][az-aks-nodepool-add], puis spécifier `--zones` pour ces nouveaux nœuds, mais les composants du plan de contrôle ne tiendront pas compte des zones de disponibilité. Vous ne pouvez ni modifier la prise en compte des zones d’un pool de nœuds ni les composants du plan de contrôle AKS une fois ces éléments déployés.

L’exemple suivant crée un cluster AKS nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*. Un total de *3* nœuds sont créés : un agent dans la zone *1*, un dans la zone *2*, puis un dans la zone *3*. Les composants du plan de contrôle AKS sont également répartis entre les zones dans la configuration la plus élevée possible puisqu'ils sont définis dans le cadre du processus de création du cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

La création du cluster AKS ne prend que quelques minutes.

## <a name="verify-node-distribution-across-zones"></a>Vérifier la distribution des nœuds entre les zones

Lorsque le cluster est prêt, répertoriez les nœuds d'agent dans l’échelle définie pour voir dans quelle zone de disponibilité ils sont déployés.

Tout d’abord, obtenez les informations d’identification du cluster AKS à l’aide de la commande [az aks get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Utilisez ensuite la commande [kubectl describe][kubectl-describe] pour répertorier les nœuds du cluster. Filtrez sur valeur *failure-domain.beta.kubernetes.io/zone* comme indiqué dans l'exemple suivant :

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

L'exemple de sortie suivant montre les trois nœuds répartis dans la région et les zones de disponibilité spécifiées, notamment *eastus2-1* pour la première zone de disponibilité et *eastus2-2* pour la deuxième zone de disponibilité :

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Lorsque vous ajoutez des nœuds supplémentaires à un pool d'agents, la plate-forme Azure distribue automatiquement les machines virtuelles sous-jacentes entre les zones de disponibilité spécifiées.

Notez que dans les versions plus récentes de Kubernetes (1.17.0 et ultérieures), AKS utilise l’étiquette plus récente `topology.kubernetes.io/zone` en plus de l’étiquette dépréciée `failure-domain.beta.kubernetes.io/zone`.

## <a name="verify-pod-distribution-across-zones"></a>Vérifier la distribution des pods entre les zones

Comme décrit dans [Étiquettes, annotations et non-affinités connues][kubectl-well_known_labels], Kubernetes utilise l’étiquette `failure-domain.beta.kubernetes.io/zone` pour distribuer automatiquement les pods dans un contrôleur ou un service de réplication entre les différentes zones disponibles. Pour tester cela, vous pouvez effectuer un scale-up de votre cluster de 3 à 5 nœuds, pour vérifier que la propagation du pod est correcte :

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Quand l’opération de mise à l’échelle se termine au bout de quelques minutes, la commande `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` doit produire une sortie similaire à cet exemple :

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Comme vous pouvez le voir, nous avons maintenant deux nœuds supplémentaires dans les zones 1 et 2. Vous pouvez déployer une application composée de trois réplicas. Nous allons utiliser NGINX comme exemple :

```console
kubectl run nginx --image=nginx --replicas=3
```

Si vous vérifiez que les nœuds où se trouvent vos pods sont en cours d’exécution, vous voyez que les pods s’exécutent sur les pods correspondant aux trois zones de disponibilité différentes. Par exemple, avec la commande `kubectl describe pod | grep -e "^Name:" -e "^Node:"` vous obtenez une sortie similaire à celle-ci :

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Comme vous pouvez le voir dans la sortie précédente, le premier pod s’exécute sur le nœud 0, qui se trouve dans la zone de disponibilité `eastus2-1`. Le deuxième pod s’exécute sur le nœud 2, qui correspond à `eastus2-3`, et le troisième dans le nœud 4, dans `eastus2-2`. Sans aucune configuration supplémentaire, Kubernetes répartit correctement les pods entre les trois zones de disponibilité.

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment créer un cluster AKS qui utilise des zones de disponibilité. Pour plus d’informations sur les clusters à haute disponibilité, consultez [Best practices for business continuity and disaster recovery in AKS][best-practices-bc-dr] (Meilleures pratiques pour la continuité d’activité et la récupération d’urgence dans AKS).

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
