---
title: Mise en réseau avancée configurée dans AKS (Azure Kubernetes Service)
description: Découvrez comment configurer le réseau avancé dans Azure Kubernetes Service (AKS), y compris pour déployer un cluster AKS dans un réseau virtuel et un sous-réseau.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 4c60474c07a3853e409436359713578178b639fb
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024853"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>Configurer la mise en réseau avancée dans AKS (Azure Kubernetes Service)

Par défaut, les clusters AKS utilisent la mise en réseau *de base*, qui crée et configure un réseau virtuel et un sous-réseau pour une utilisation avec le cluster. Pour un contrôle supplémentaire de ces options de mise en réseau, par exemple les plages d’adresses IP, vous pouvez utiliser la mise en réseau *avancée*. Avec la mise en réseau avancée, vous pouvez également créer un cluster AKS dans un réseau virtuel et un sous-réseau. Ce réseau virtuel existant fournit souvent la connectivité à un réseau local avec Azure ExpressRoute ou un VPN de site à site.

Cet article vous montre comment utiliser la mise en réseau avancée pour créer et utiliser un réseau virtuel avec un cluster AKS. Pour obtenir des informations plus générales sur la mise en réseau, consultez [Concepts réseau de Kubernetes et AKS][aks-network-concepts].

## <a name="prerequisites"></a>Prérequis

* Le réseau virtuel du cluster AKS doit autoriser les connexions Internet sortantes.
* Ne créez pas plus d’un cluster AKS dans le même sous-réseau.
* Les clusters AKS ne peuvent pas utiliser `169.254.0.0/16`, `172.30.0.0/16` ou `172.31.0.0/16` pour la plage d’adresses de service Kubernetes.
* Le principal du service utilisé par le cluster AKS doit disposer au moins des autorisations [Contributeur de réseau](../role-based-access-control/built-in-roles.md#network-contributor) sur le sous-réseau de votre réseau virtuel. Si vous souhaitez définir un [rôle personnalisé](../role-based-access-control/custom-roles.md) au lieu d’utiliser le rôle de contributeur de réseau intégré, les autorisations suivantes sont nécessaires :
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planifier l’adressage IP pour votre cluster

Les clusters configurés avec la mise en réseau avancée nécessitent une planification supplémentaire. La taille de votre réseau virtuel et de son sous-réseau doit être suffisante pour prendre en charge le nombre de pods que vous envisagez d’exécuter, ainsi que le nombre de nœuds du cluster.

Les adresses IP des pods et des nœuds de cluster sont affectées à partir du sous-réseau spécifié du réseau virtuel. Chaque nœud est configuré avec une adresse IP principale. Par défaut, 30 adresses IP supplémentaires sont préconfigurés par CNI Azure et affectées à des pods planifiés sur le nœud. Lorsque vous faites monter en charge votre cluster, chaque nœud est configuré de manière similaire avec des adresses IP du sous-réseau. Vous pouvez également voir le nombre [maximal de pods par nœud](#maximum-pods-per-node).

Le plan d’adressage IP pour un cluster AKS se compose d’un réseau virtuel, d’au moins un sous-réseau pour les nœuds et les pods, et d’une plage d’adresses de service Kubernetes.

| Plage Azure/ressource Azure | Limites et tailles |
| --------- | ------------- |
| Réseau virtuel | Le réseau virtuel Azure peut être aussi volumineux que la valeur /8, mais est limité à 65 536 adresses IP configurées. |
| Sous-réseau | Doit pouvoir contenir les nœuds, les pods, ainsi que toutes les ressources Kubernetes et Azure qui peuvent être provisionnées dans votre cluster. Par exemple, si vous déployez un équilibreur de charge interne Azure, ses adresses IP frontend sont allouées à partir du sous-réseau du cluster, et non à partir des adresses IP non publiques. <p/>Pour calculer la taille *minimale* du sous-réseau : `(number of nodes) + (number of nodes * maximum pods per node that you configure)` <p/>Exemple pour un cluster à 50 nœuds : `(50) + (50 * 30 (default)) = 1,550` (/21 ou plus)<p>Si vous ne spécifiez pas de nombre maximal de pods par nœud lorsque vous créez votre cluster, le nombre maximal de pods par nœud est de *30*. Le nombre minimal d’adresses IP requises est basé sur cette valeur. Si vous calculez vos exigences d’adresse IP minimales sur une autre valeur maximale, consultez [comment configurer le nombre maximal de pods par nœud](#configure-maximum---new-clusters) pour définir cette valeur lorsque vous déployez votre cluster. |
| Plage d’adresses de service Kubernetes | Cette plage ne doit être utilisée par aucun élément réseau sur ce réseau virtuel ou connecté à celui-ci. Le CIDR d’adresse du service doit être inférieur à /12. |
| Adresse IP du service DNS Kubernetes | Adresse IP dans la plage d’adresses de service Kubernetes, qui sera utilisée par la détection de service de cluster (kube-dns). |
| Adresse de pont Docker | Adresse IP (en notation CIDR) utilisée en tant qu’adresse IP de pont Docker sur les nœuds. Valeur par défaut : 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Nombre maximal de pods par nœud

Le nombre maximal de pods par nœud dans un cluster AKS est de 110. Le nombre maximal *par défaut* de pods par nœud varie selon qu’il s’agit d’un réseau de base ou avancé, et selon la méthode de déploiement du cluster.

| Méthode de déploiement | Par défaut de base | Par défaut avancé | Configurable au moment du déploiement |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Oui (jusqu’à 110) |
| Modèle Resource Manager | 110 | 30 | Oui (jusqu’à 110) |
| Portail | 110 | 30 | Non  |

### <a name="configure-maximum---new-clusters"></a>Configurer un maximum : nouveaux clusters

Vous pouvez configurer le nombre maximal de pods par nœud *uniquement au moment du déploiement cluster*. Si vous procédez au déploiement avec Azure CLI ou avec un modèle Resource Manager, vous pouvez définir la valeur du nombre maximal de pods par nœud avec un maximum de 110.

* **Azure CLI** : Spécifiez l’argument `--max-pods` lorsque vous déployez un cluster avec la commande [az aks create][az-aks-create]. La valeur maximale est 110.
* **Modèle Resource Manager** : Spécifiez la propriété `maxPods` dans l’objet [ManagedClusterAgentPoolProfile] lorsque vous déployez un cluster avec un modèle Resource Manager. La valeur maximale est 110.
* **Portail Azure** : vous ne pouvez pas modifier le nombre maximal de pods par nœud lorsque vous déployez un cluster avec le portail Azure. Les clusters de réseau avancé sont limités à 30 pods par nœud lorsque vous effectuez le déploiement avec le portail Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurer un maximum : clusters existants

Vous ne pouvez pas modifier le nombre maximal de pods par nœud sur un cluster AKS existant. Vous pouvez ajuster le nombre uniquement lors du déploiement initial du cluster.

## <a name="deployment-parameters"></a>Paramètres de déploiement

Lorsque vous créez un cluster AKS, les paramètres suivants sont configurables pour les réseaux avancés :

**Réseau virtuel** : réseau virtuel dans lequel vous souhaitez déployer le cluster Kubernetes. Si vous souhaitez créer un réseau virtuel pour votre cluster, sélectionnez *Créer un nouveau*, puis suivez la procédure décrite dans la section *Créer un réseau virtuel*. Pour plus d’informations sur les limites et quotas d’un réseau virtuel Azure, voir [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sous-réseau** : sous-réseau du réseau virtuel dans lequel vous souhaitez déployer le cluster. Si vous souhaitez créer un nouveau sous-réseau dans le réseau virtuel pour votre cluster, sélectionnez *Créer un nouveau*, puis exécutez la procédure décrite dans la section *Créer un sous-réseau*.

**Plage d’adresses du service Kubernetes** : il s’agit du jeu d’adresses IP virtuelles que Kubernetes affecte aux [services][services] dans votre cluster. Vous pouvez utiliser n’importe quelle plage d’adresses privées répondant aux exigences suivantes :

* ne peut pas figurer dans la plage d’adresses IP de réseau virtuel de votre cluster ;
* ne peut pas présenter de chevauchement avec un autre réseau virtuel avec lequel le réseau virtuel du cluster s’apparie ;
* ne doit avoir aucun élément en commun avec des adresses IP locales ;
* ne doit pas être dans les plages `169.254.0.0/16`, `172.30.0.0/16`, ou `172.31.0.0/16`.

Bien qu’il soit techniquement possible de spécifier une plage d’adresses de service dans le même réseau virtuel que votre cluster, cette manière de procéder n’est pas recommandée. Tout chevauchement entre des plages d’adresses IP est susceptible d’entraîner des comportements imprévisibles. Pour plus d’informations, consultez la section [FAQ](#frequently-asked-questions) de cet article. Pour plus d’informations sur les réplicas Kubernetes, consultez[Services][services] dans la documentation de Kubernetes.

**Adresse IP du service DNS Kubernetes** :l’adresse IP du service DNS du cluster. Cette adresse doit se situer dans la *plage d’adresses du service Kubernetes*.

**Adresse du pont docker** : l’adresse IP et le masque réseau à affecter au pont docker. Cette adresse IP ne peut pas figurer dans la plage d’adresses IP de réseau virtuel de votre cluster.

## <a name="configure-networking---cli"></a>Configurer la mise en réseau – Interface de ligne de commande

Lors de la création d’un cluster AKS avec Azure CLI, il est également possible de configurer la mise en réseau avancée. Utilisez les commandes suivantes pour créer un nouveau cluster AKS en activant les fonctionnalités de mise en réseau avancée.

Tout d’abord, récupérez l’ID de la ressource du sous-réseau auquel le cluster AKS sera joint :

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Utilisez la commande [az aks create][az-aks-create] avec l’argument `--network-plugin azure` pour créer un cluster avec mise en réseau avancée. Remplacez la valeur `--vnet-subnet-id` par l’ID du sous-réseau recueilli à l’étape précédente :

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Configurer la mise en réseau – Portail

La capture d’écran suivante de votre portail Azure représente un exemple de configuration de ces paramètres durant la création du cluster AKS :

![Configuration de la mise en réseau avancée dans le portail Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

La série suivante de questions-réponses s’applique à la configuration réseau **avancée**.

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

  Cette configuration, bien que possible, n’est pas recommandée. La plage d’adresses du service est un jeu d’adresses IP virtuelles que Kubernetes affecte aux services dans votre cluster. Azure Networking ne peut pas voir la plage d’adresses IP de service du cluster Kubernetes. En raison de ce manque de visibilité, il est possible de créer ultérieurement un sous-réseau dans le réseau virtuel du cluster, qui chevauche la plage d’adresses de service. Si un chevauchement de ce type se produit, Kubernetes peut affecter à un service une adresse IP déjà utilisée par une autre ressource dans le sous-réseau, ce qui provoque un comportement imprévisible ou des échecs. Utilisez une plage d’adresses en dehors du réseau virtuel du cluster pour éviter tout risque de chevauchement.

## <a name="next-steps"></a>Étapes suivantes

### <a name="networking-in-aks"></a>Mise en réseau dans AKS

Pour plus d’informations sur la mise en réseau dans AKS, consultez les articles suivants :

- [Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS)](static-ip.md)
- [Utiliser un équilibreur de charge interne avec Azure Container Service (AKS)](internal-lb.md)

- [Créer un contrôleur d’entrée de base avec une connectivité réseau externe][aks-ingress-basic]
- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- [Créer un contrôleur d’entrée avec une adresse IP publique dynamique et configurer Let’s Encrypt pour générer automatiquement des certificats TLS][aks-ingress-tls]
- [Créer un contrôleur d’entrée avec une adresse IP publique statique et configurer Let’s Encrypt pour générer automatiquement des certificats TLS][aks-ingress-static-tls]

### <a name="acs-engine"></a>Moteur ACS

Le [moteur Azure Container Service (moteur ACS)][acs-engine] est un projet open source générant des modèles Azure Resource Manager que vous pouvez utiliser pour déployer des clusters Docker sur Azure. Kubernetes, DC/OS, le mode Swarm et les orchestrateurs Swarm peuvent être déployés avec le moteur ACS.

Les clusters Kubernetes créés avec le moteur ACS prennent en charge les plug-ins [kubenet][kubenet] et [Azure CNI][cni-networking]. En l’état, les scénarios de mise en réseau de base et avancée sont pris en charge par le moteur ACS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
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
