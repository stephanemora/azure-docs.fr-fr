---
title: Utiliser des zones de disponibilité dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer un cluster qui distribue les nœuds entre les zones de disponibilité dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840457"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Préversion - Créer un cluster Azure Kubernetes Service (AKS) qui utilise des zones de disponibilité

Un cluster AKS (Azure Kubernetes Service) répartit les ressources telles que les nœuds et le stockage entre les sections logiques de l'infrastructure de calcul Azure sous-jacente. Ce modèle de déploiement permet de s'assurer que les nœuds s'exécutent sur des domaines de mise à jour et d’erreur distincts d’un même centre de données Azure. Les clusters AKS déployés avec ce comportement par défaut offrent un niveau élevé de disponibilité pour se protéger contre une panne matérielle ou un événement de maintenance planifiée.

Pour offrir un niveau de disponibilité plus élevé à vos applications, les clusters AKS peuvent être répartis sur plusieurs zones de disponibilité. Ces zones représentent des centres de données physiquement séparés au sein d’une région donnée. Lorsque les composants du cluster sont répartis sur plusieurs zones, votre cluster AKS est capable de tolérer une défaillance dans l'une de ces zones. Vos applications et vos opérations de gestion restent disponibles même si un centre de données complet rencontre un problème.

Cet article vous explique comment créer un cluster AKS et répartir les composants de nœud entre des zones de disponibilité. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Elles sont fournies pour que notre communauté puisse faire part de ses commentaires et des bogues éventuels. En préversion, ces fonctionnalités ne sont pas destinées à une utilisation en production. Les fonctionnalités en préversion publique font l’objet d’un support relatif. L’assistance des équipes de support technique AKS est disponible pendant les heures de bureau du fuseau horaire Heure du Pacifique uniquement. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

## <a name="before-you-begin"></a>Avant de commencer

Azure CLI 2.0.66 (ou version ultérieure) doit être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Pour créer des clusters AKS qui utilisent des zones de disponibilité, vous avez besoin de l’extension de l’interface CLI *aks-preview* 0.4.1 ou version supérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Inscrire des indicateurs de fonctionnalité pour votre abonnement

Afin de créer un cluster AKS pour des zones de disponibilité, commencez par activer des indicateurs de fonctionnalité sur votre abonnement. Les clusters utilisent un groupe de machines virtuelles identiques pour gérer le déploiement et la configuration des nœuds Kubernetes. La référence SKU *standard* de l'équilibreur de charge Azure est également requise pour assurer la résilience des composants réseau afin de diriger le trafic vers votre cluster. Inscrivez les indicateurs de fonctionnalité *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer* et *VMSSPreview* à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités en préversion, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état de l’enregistrement à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Limitations et disponibilité de la région

Les clusters AKS peuvent actuellement être créés en utilisant des zones de disponibilité dans les régions suivantes :

* USA Est 2
* Europe Nord
* Asie Sud-Est
* Europe Ouest
* USA Ouest 2

Les limitations suivantes s'appliquent lorsque vous créez un cluster AKS à l'aide de zones de disponibilité :

* Vous ne pouvez activer les zones de disponibilité que lorsque le cluster est créé.
* Les paramètres de la zone de disponibilité ne peuvent pas être mis à jour une fois le cluster créé. Vous ne pouvez pas non plus mettre à jour un cluster de zones de non-disponibilité existant pour utiliser des zones de disponibilité.
* Vous ne pouvez pas désactiver les zones de disponibilité d’un cluster AKS une fois qu'il a été créé.
* La taille de nœud (référence SKU de la machine virtuelle) sélectionnée doit être disponible dans toutes les zones de disponibilité.
* Les clusters avec des zones de disponibilité activées nécessitent l'utilisation d'équilibreurs de charge Standard Azure pour la distribution entre les zones.
* Vous devez utiliser Kubernetes version 1.13.5 ou supérieure pour déployer des équilibreurs de charge Standard.

Les clusters AKS qui emploient des zones de disponibilité doivent utiliser la référence SKU *Standard* de l’équilibreur de charge Azure. La référence SKU *De base* de l'équilibreur de charge Azure ne prend pas en charge la distribution entre les zones de disponibilité. Pour plus d'informations et connaître les limites de l’équilibreur de charge Standard, consultez [Limites de la préversion de la référence SKU Standard de l’équilibreur de charge Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitations des disques Azure

Les volumes qui utilisent des disques managés Azure ne constituent pas actuellement des ressources zonales. Les pods replanifiés dans une zone différente de leur zone d'origine ne peuvent pas reconnecter leur(s) disque(s) précédent(s). Il est recommandé d'exécuter des charges de travail sans état qui ne nécessitent pas de stockage persistant pouvant présenter des problèmes zonaux.

Si vous devez exécuter des charges de travail sans état, utilisez les teintes et les tolérances des spécifications de vos pods pour indiquer au planificateur Kubernetes de créer des pods dans la même zone que vos disques. Vous pouvez également utiliser un stockage en réseau, par exemple Azure Files, pour vous connecter aux pods à mesure qu'ils sont planifiés entre les zones.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Vue d’ensemble des zones de disponibilité pour les clusters AKS

Les Zones de disponibilité constituent une offre à haute disponibilité qui protège vos applications et données contre les pannes des centres de données. Les zones sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, il existe un minimum de trois zones distinctes dans toutes les régions activées. La séparation physique des Zones de disponibilité dans une région protège les applications et les données des défaillances dans le centre de données. Les services redondants interzone répliquent vos applications et données entre des Zones de disponibilité pour les protéger contre des points uniques de panne.

Pour plus d’informations, consultez [Que sont les zones de disponibilité dans Azure ?][az-overview].

Les clusters AKS déployés à l'aide de zones de disponibilité peuvent répartir les nœuds sur plusieurs zones au sein d'une même région. Par exemple, un cluster dans la région  *USA Est 2*  peut créer des nœuds dans les trois zones de disponibilité de *USA Est 2*. Cette distribution des ressources du cluster AKS améliore la disponibilité du cluster car elles résistent aux défaillances d'une zone spécifique.

![Distribution des nœuds AKS entre les zones de disponibilité](media/availability-zones/aks-availability-zones.png)

En cas de défaillance d’une zone, les nœuds peuvent être rééquilibrés manuellement ou à l'aide de l'autochargeur de cluster. Si une seule zone devient indisponible, vos applications continuent de s'exécuter.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Créer un cluster AKS sur plusieurs zones de disponibilité

Lorsque vous créez un cluster à l'aide de la commande [az aks create][az-aks-create], le paramètre `--node-zones` définit dans quelles zones les nœuds d'agent sont déployés. Les composants du plan de contrôle AKS de votre cluster sont également répartis entre les zones dans la configuration la plus élevée disponible lorsque vous créez un cluster en spécifiant le paramètre `--node-zones`.

Si vous ne définissez aucune zone pour le pool d'agents par défaut lorsque vous créez un cluster AKS, les composants du plan de contrôle AKS de votre cluster n'utiliseront aucune zone de disponibilité. Vous pouvez ajouter des pools de nœuds supplémentaires (actuellement en préversion dans AKS) en utilisant la commande [az aks nodepool add][az-aks-nodepool-add], puis spécifiez `--node-zones` pour ces nouveaux nœuds d'agents, mais les composants du plan de contrôle ne tiendront pas compte des zones de disponibilité. Vous ne pouvez ni modifier la prise en compte des zones d’un pool de nœuds ni les composants du plan de contrôle AKS une fois ces éléments déployés.

L’exemple suivant crée un cluster AKS nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*. Un total de *3* nœuds sont créés : un agent dans la zone *1*, un dans la zone *2*, puis un dans la zone *3*. Les composants du plan de contrôle AKS sont également répartis entre les zones dans la configuration la plus élevée possible puisqu'ils sont définis dans le cadre du processus de création du cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
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
