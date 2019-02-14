---
title: Configurer un réseau Azure CNI dans AKS (Azure Kubernetes Service)
description: Découvrez comment configurer un réseau (avancé) Azure CNI dans AKS (Azure Kubernetes Service), notamment pour déployer un cluster AKS dans un réseau et un sous-réseau virtuels existants.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 7d91366ee0fec2930484f7aaa7468e6d1d62f233
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701585"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurer un réseau Azure CNI dans AKS (Azure Kubernetes Service)

Par défaut, les clusters AKS utilisent [kubenet][kubenet], et un réseau et un sous-réseau virtuels sont automatiquement créés. Avec *kubenet*, les nœuds obtiennent une adresse IP d’un sous-réseau du réseau virtuel. La traduction d’adresses réseau (NAT) est ensuite configurée sur les nœuds, et les pods reçoivent une adresse IP « cachée » derrière l’adresse IP du nœud. Cette approche réduit le nombre d’adresses IP que vous avez besoin de réserver dans votre espace réseau à l’usage des pods.

Avec l’interface [Azure Container Networking Interface (CNI)][cni-networking], chaque pod reçoit une adresse IP du sous-réseau et est accessible directement. Ces adresses IP doivent être uniques dans votre espace réseau, et être planifiées à l’avance. Chaque nœud a un paramètre de configuration qui définit le nombre maximal de pods qu’il prend en charge. Le nombre équivalent d’adresses IP par nœud est alors réservé à l’avance pour ce nœud. Cette approche demande un plus grand travail de planification. De plus, elle conduit souvent à l’épuisement des adresses IP ou à la nécessité de regénérer les clusters dans un sous-réseau plus vaste à mesure que vos besoins d’applications augmentent.

Cet article vous montre comment utiliser les réseaux *Azure CNI* afin de créer et d’utiliser un sous-réseau de réseau virtuel pour un cluster AKS. Pour plus d’informations sur les options et considérations relatives aux réseaux, consultez [Concepts de réseau pour Kubernetes et AKS][aks-network-concepts].

## <a name="prerequisites"></a>Prérequis

* Le réseau virtuel du cluster AKS doit autoriser les connexions Internet sortantes.
* Ne créez pas plus d’un cluster AKS dans le même sous-réseau.
* Les clusters AKS ne peuvent pas utiliser `169.254.0.0/16`, `172.30.0.0/16` ou `172.31.0.0/16` pour la plage d’adresses de service Kubernetes.
* Le principal du service utilisé par le cluster AKS doit disposer au moins des autorisations [Contributeur de réseau](../role-based-access-control/built-in-roles.md#network-contributor) sur le sous-réseau de votre réseau virtuel. Si vous souhaitez définir un [rôle personnalisé](../role-based-access-control/custom-roles.md) au lieu d’utiliser le rôle de contributeur de réseau intégré, les autorisations suivantes sont nécessaires :
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planifier l’adressage IP pour votre cluster

Les clusters configurés avec les réseaux Azure CNI nécessitent une planification supplémentaire. La taille de votre réseau virtuel et de son sous-réseau doit être suffisante pour prendre en charge le nombre de pods que vous envisagez d’exécuter, ainsi que le nombre de nœuds du cluster.

Les adresses IP des pods et des nœuds de cluster sont affectées à partir du sous-réseau spécifié du réseau virtuel. Chaque nœud est configuré avec une adresse IP principale. Par défaut, 30 adresses IP supplémentaires sont préconfigurées par CNI Azure et affectées à des pods planifiés sur le nœud. Lorsque vous faites monter en charge votre cluster, chaque nœud est configuré de manière similaire avec des adresses IP du sous-réseau. Vous pouvez également voir le nombre [maximal de pods par nœud](#maximum-pods-per-node).

> [!IMPORTANT]
> Le nombre d’adresses IP requises doit prendre en compte des considérations relatives aux opérations de mise à niveau et à l’échelle. Si vous définissez la plage d’adresses IP pour prendre en charge uniquement un nombre fixe de nœuds, vous ne pouvez pas mettre à niveau ou à l’échelle votre cluster.
>
> - Lorsque vous **mettez à niveau** votre cluster AKS, un nouveau nœud est déployé dans le cluster. Les services et charges de travail commencent à s’exécuter sur le nouveau nœud, et le nœud plus ancien est supprimé du cluster. Ce processus de mise à niveau propagée nécessite la disponibilité d’un minimum d’adresses IP ou de bloc supplémentaires. Le nombre de nœuds est alors `n + 1`.
>
> - Lorsque vous **mettez à l’échelle** un cluster AKS, un nouveau nœud est déployé dans le cluster. Les services et charges de travail commencent à s’exécuter sur le nouveau nœud. Votre plage d’adresses IP doit prendre en compte la manière dont vous voulez augmenter le nombre de nœuds et de pods que votre cluster prend en charge. Un nœud supplémentaire pour les opérations de mise à niveau doit également être inclus. Le nombre de nœuds est alors `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Si vous vous attendez à ce que vos nœuds exécutent le nombre maximal de pods, et détruisent et déploient régulièrement des pods, vous devez également prendre en compte des adresses IP supplémentaires par nœud. Ces adresses IP supplémentaires tiennent compte du fait que la suppression d’un service et la libération de l’adresse IP pour le déploiement et l’acquisition de l’adresse d’un nouveau service peuvent prendre quelques secondes.

Le plan d’adressage IP pour un cluster AKS se compose d’un réseau virtuel, d’au moins un sous-réseau pour les nœuds et les pods, et d’une plage d’adresses de service Kubernetes.

| Plage Azure/ressource Azure | Limites et tailles |
| --------- | ------------- |
| Réseau virtuel | Le réseau virtuel Azure peut être aussi volumineux que la valeur /8, mais est limité à 65 536 adresses IP configurées. |
| Sous-réseau | Doit pouvoir contenir les nœuds, les pods, ainsi que toutes les ressources Kubernetes et Azure qui peuvent être provisionnées dans votre cluster. Par exemple, si vous déployez un équilibreur de charge interne Azure, ses adresses IP frontend sont allouées à partir du sous-réseau du cluster, et non à partir des adresses IP non publiques. La taille du sous-réseau doit également prendre en compte les opérations de mise à niveau ou de futurs besoins de mise à l’échelle.<p />Pour calculer la taille de sous-réseau *minimale*, dont celle d’un nœud supplémentaire pour les opérations de mise à niveau : `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemple pour un cluster à 50 nœuds : `(51) + (51  * 30 (default)) = 1,581` (/21 ou plus)<p/>Exemple pour un cluster de 50 nœuds incluant également un approvisionnement pour porter l’échelle à 10 nœuds supplémentaires : `(61) + (61 * 30 (default)) = 1,891` (/21 ou plus)<p>Si vous ne spécifiez pas de nombre maximal de pods par nœud lorsque vous créez votre cluster, le nombre maximal de pods par nœud est de *30*. Le nombre minimal d’adresses IP requises est basé sur cette valeur. Si vous calculez vos exigences d’adresse IP minimales sur une autre valeur maximale, consultez [comment configurer le nombre maximal de pods par nœud](#configure-maximum---new-clusters) pour définir cette valeur lorsque vous déployez votre cluster. |
| Plage d’adresses de service Kubernetes | Cette plage ne doit être utilisée par aucun élément réseau sur ce réseau virtuel ou connecté à celui-ci. Le CIDR d’adresse du service doit être inférieur à /12. |
| Adresse IP du service DNS Kubernetes | Adresse IP dans la plage d’adresses de service Kubernetes, qui sera utilisée par la détection de service de cluster (kube-dns). N’utilisez pas la première adresse IP de votre plage d’adresses (1, par exemple). La première adresse de votre plage de sous-réseaux est utilisée pour l’adresse *kubernetes.default.svc.cluster.local*. |
| Adresse de pont Docker | Adresse IP (en notation CIDR) utilisée en tant qu’adresse IP de pont Docker sur les nœuds. Valeur par défaut : 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Nombre maximal de pods par nœud

Le nombre maximal de pods par nœud dans un cluster AKS est de 110. Le nombre maximal *par défaut* de pods par nœud varie selon qu’il s’agit d’un réseau *kubenet* ou *Azure CNI*, mais aussi en fonction de la méthode de déploiement du cluster.

| Méthode de déploiement | Kubenet par défaut | Azure CNI par défaut | Configurable au moment du déploiement |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Oui (jusqu’à 110) |
| Modèle Resource Manager | 110 | 30 | Oui (jusqu’à 110) |
| Portail | 110 | 30 | Non  |

### <a name="configure-maximum---new-clusters"></a>Configurer un maximum : nouveaux clusters

Vous pouvez configurer le nombre maximal de pods par nœud *uniquement au moment du déploiement cluster*. Si vous procédez au déploiement avec Azure CLI ou avec un modèle Resource Manager, vous pouvez définir la valeur du nombre maximal de pods par nœud avec un maximum de 110.

* **Azure CLI** : spécifiez l’argument `--max-pods` lorsque vous déployez un cluster avec la commande [az aks create][az-aks-create]. La valeur maximale est 110.
* **Modèle Resource Manager** : spécifiez la propriété `maxPods` dans l’objet [ManagedClusterAgentPoolProfile] lorsque vous déployez un cluster avec un modèle Resource Manager. La valeur maximale est 110.
* **Portail Azure**: vous ne pouvez pas modifier le nombre maximal de pods par nœud lorsque vous déployez un cluster avec le portail Azure. Les clusters de réseau Azure CNI sont limités à 30 pods par nœud quand vous effectuez le déploiement à partir du portail Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurer un maximum : clusters existants

Vous ne pouvez pas modifier le nombre maximal de pods par nœud sur un cluster AKS existant. Vous pouvez ajuster le nombre uniquement lors du déploiement initial du cluster.

## <a name="deployment-parameters"></a>Paramètres de déploiement

Quand vous créez un cluster AKS, les paramètres suivants sont configurables pour les réseaux Azure CNI :

**Réseau virtuel** : réseau virtuel dans lequel vous souhaitez déployer le cluster Kubernetes. Si vous souhaitez créer un réseau virtuel pour votre cluster, sélectionnez *Créer un nouveau*, puis suivez la procédure décrite dans la section *Créer un réseau virtuel*. Pour plus d’informations sur les limites et quotas d’un réseau virtuel Azure, voir [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sous-réseau** : sous-réseau du réseau virtuel dans lequel vous souhaitez déployer le cluster. Si vous souhaitez créer un nouveau sous-réseau dans le réseau virtuel pour votre cluster, sélectionnez *Créer un nouveau*, puis exécutez la procédure décrite dans la section *Créer un sous-réseau*. Pour une connectivité hybride, la plage d’adresses ne doit pas chevaucher d’autres réseaux virtuels dans votre environnement.

**Plage d’adresses du service Kubernetes** : il s’agit du jeu d’adresses IP virtuelles que Kubernetes attribue aux [services][services] internes dans votre cluster. Vous pouvez utiliser n’importe quelle plage d’adresses privées répondant aux exigences suivantes :

* ne peut pas figurer dans la plage d’adresses IP de réseau virtuel de votre cluster ;
* ne peut pas présenter de chevauchement avec un autre réseau virtuel avec lequel le réseau virtuel du cluster s’apparie ;
* ne doit avoir aucun élément en commun avec des adresses IP locales ;
* ne doit pas être dans les plages `169.254.0.0/16`, `172.30.0.0/16`, ou `172.31.0.0/16`.

Bien qu’il soit techniquement possible de spécifier une plage d’adresses de service dans le même réseau virtuel que votre cluster, cette manière de procéder n’est pas recommandée. Tout chevauchement entre des plages d’adresses IP est susceptible d’entraîner des comportements imprévisibles. Pour plus d’informations, consultez la section [FAQ](#frequently-asked-questions) de cet article. Pour plus d’informations sur les réplicas Kubernetes, consultez[Services][services] dans la documentation de Kubernetes.

**Adresse IP du service DNS Kubernetes** :  adresse IP du service DNS du cluster. Cette adresse doit se situer dans la *plage d’adresses du service Kubernetes*. N’utilisez pas la première adresse IP de votre plage d’adresses (1, par exemple). La première adresse de votre plage de sous-réseaux est utilisée pour l’adresse *kubernetes.default.svc.cluster.local*.

**Adresse du pont Docker** : adresse IP et masque de réseau assignés au pont Docker. Le pont Docker permet aux nœuds AKS de communiquer avec la plateforme de gestion sous-jacente. Cette adresse IP ne doit pas être dans la plage d’adresses IP du réseau virtuel de votre cluster, ni chevaucher d’autres plages d’adresses actuellement utilisées sur votre réseau.

## <a name="configure-networking---cli"></a>Configurer le réseau – Interface de ligne de commande

Lors de la création d’un cluster AKS avec Azure CLI, il est également possible de configurer un réseau Azure CNI. Utilisez les commandes suivantes pour créer un cluster AKS en activant un réseau Azure CNI.

Tout d’abord, récupérez l’ID de la ressource du sous-réseau auquel le cluster AKS sera joint :

```console
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Utilisez la commande [az aks create][az-aks-create] avec l’argument `--network-plugin azure` pour créer un cluster avec un réseau avancé. Remplacez la valeur `--vnet-subnet-id` par l’ID du sous-réseau recueilli à l’étape précédente :

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Configurer le réseau – Portail

La capture d’écran suivante de votre portail Azure représente un exemple de configuration de ces paramètres durant la création du cluster AKS :

![Configuration du réseau avancé dans le portail Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

La série suivante de questions-réponses s’applique à la configuration de réseaux **Azure CNI**.

* *Puis-je déployer des machines virtuelles dans le sous-réseau de mon cluster ?*

   Non. Le déploiement des machines virtuelles dans le sous-réseau utilisé par votre cluster Kubernetes n’est pas pris en charge. Les machines virtuelles peuvent être déployées dans le même réseau virtuel, mais dans un sous-réseau différent.

* *Puis-je configurer des stratégies de réseau spécifiques aux pods ?*

   Non. Les stratégies de réseau spécifiques aux pods ne sont actuellement pas prises en charge.

* *Le nombre maximal de pods pouvant être déployés sur un nœud peut-il être configuré ?*

  Oui, lorsque vous déployez un cluster avec l’interface CLI Azure ou un modèle Resource Manager. Consultez [Nombre maximal de pods par nœud](#maximum-pods-per-node).

  Vous ne pouvez pas modifier le nombre maximal de pods par nœud sur un cluster existant.

* *Comment configurer des propriétés supplémentaires pour le sous-réseau développé lors de la création du cluster AKS ? Par exemple, des points de terminaison du service.*

  La liste complète des propriétés pour le réseau virtuel et les sous- réseaux développés durant la création du cluster AKS peut être configurée dans la page de configuration du réseau virtuel standard du portail Azure.

* *Puis-je utiliser un autre sous-réseau dans le réseau virtuel de mon cluster pour la* **plage d’adresses du service Kubernetes** ?

  Cette configuration, bien que possible, n’est pas recommandée. La plage d’adresses du service est un jeu d’adresses IP virtuelles que Kubernetes attribue aux services internes dans votre cluster. Azure Networking ne peut pas voir la plage d’adresses IP de service du cluster Kubernetes. En raison de ce manque de visibilité, il est possible de créer ultérieurement un sous-réseau dans le réseau virtuel du cluster, qui chevauche la plage d’adresses de service. Si un chevauchement de ce type se produit, Kubernetes peut affecter à un service une adresse IP déjà utilisée par une autre ressource dans le sous-réseau, ce qui provoque un comportement imprévisible ou des échecs. Utilisez une plage d’adresses en dehors du réseau virtuel du cluster pour éviter tout risque de chevauchement.

## <a name="next-steps"></a>Étapes suivantes

### <a name="networking-in-aks"></a>Réseaux dans AKS

Pour plus d’informations sur les réseaux dans AKS, consultez les articles suivants :

- [Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS)](static-ip.md)
- [Utiliser un équilibreur de charge interne avec Azure Container Service (AKS)](internal-lb.md)

- [Créer un contrôleur d’entrée de base avec une connectivité réseau externe][aks-ingress-basic]
- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- [Créer un contrôleur d’entrée avec une adresse IP publique dynamique et configurer Let’s Encrypt pour générer automatiquement des certificats TLS][aks-ingress-tls]
- [Créer un contrôleur d’entrée avec une adresse IP publique statique et configurer Let’s Encrypt pour générer automatiquement des certificats TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Moteur AKS

Le [moteur Azure Kubernetes Service (moteur AKS)][aks-engine] est un projet open source générant des modèles Azure Resource Manager que vous pouvez utiliser pour déployer des clusters Kubernetes sur Azure.

Les clusters Kubernetes créés avec le moteur AKS prennent en charge les plug-ins [kubenet][kubenet] et [Azure CNI][cni-networking]. En l’état, les deux scénarios de réseau sont pris en charge par le moteur AKS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
