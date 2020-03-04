---
title: Créer un cluster Azure Kubernetes Service privé
description: Découvrez comment créer un cluster Azure Kubernetes Service (AKS) privé
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 4b4ba130d9ff63291abdd46617b0692e844a60bf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649505"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Créer un cluster Azure Kubernetes Service privé (préversion)

Dans un cluster privé, le plan de contrôle ou le serveur d’API a des adresses IP internes qui sont définies dans le document [RFC1918 - Address Allocation for Private Internets](https://tools.ietf.org/html/rfc1918) (RFC1918 - Allocation d’adresses pour les réseaux Internet privés). En utilisant un cluster privé, vous pouvez garantir que le trafic réseau entre votre serveur d’API et vos pools de nœuds reste uniquement sur le réseau privé.

Le plan de contrôle ou le serveur d’API se trouve dans un abonnement Azure géré par Azure Kubernetes Service (AKS). Le cluster ou le pool de nœuds d’un client se trouve dans l’abonnement du client. Le serveur et le cluster ou le pool de nœuds peuvent communiquer entre eux par le biais du [service Azure Private Link][private-link-service] dans le réseau virtuel du serveur d’API et d’un point de terminaison privé exposé dans le sous-réseau du cluster AKS du client.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et sont proposées sur la base d’un abonnement. Les préversions sont fournies *en l’état* et *en fonction des disponibilités*. De plus, elles sont exclues du contrat de niveau de service (SLA) et de la garantie limitée. Les préversions AKS sont, *dans la mesure du possible*, partiellement couvertes par le service clientèle. Par conséquent, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour plus d’informations, consultez les articles de support suivants :
>
> * [Stratégies de support AKS](support-policies.md)
> * [FAQ du support Azure](faq.md)

## <a name="prerequisites"></a>Prérequis

* Azure CLI version 2.0.77 ou ultérieure et l’extension Azure CLI AKS version 0.4.18 en préversion

## <a name="currently-supported-regions"></a>Régions actuellement prises en charge

* Australie Est
* Sud-Australie Est
* Brésil Sud
* Centre du Canada
* Est du Canada
* USA Centre
* Asie Est
* USA Est
* USA Est 2
* USA Est 2 (EUAP)
* France Centre
* Allemagne Nord
* Japon Est
* OuJapon Est
* Centre de la Corée
* Corée du Sud
* Centre-Nord des États-Unis
* Europe Nord
* Europe Nord
* États-Unis - partie centrale méridionale
* Sud du Royaume-Uni
* Europe Ouest
* USA Ouest
* USA Ouest 2
* USA Est 2

## <a name="currently-supported-availability-zones"></a>Zones de disponibilité actuellement prises en charge

* USA Centre
* USA Est
* USA Est 2
* France Centre
* Japon Est
* Europe Nord
* Asie Sud-Est
* Sud du Royaume-Uni
* Europe Ouest
* USA Ouest 2

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installer la dernière extension Azure CLI AKS en préversion

Pour utiliser des clusters privés, vous devez disposer de l’extension Azure CLI AKS version 0.4.18 ou ultérieure en préversion. Installez l’extension Azure CLI AKS en préversion à l’aide de la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] suivante :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé certaines fonctionnalités d’évaluation, vous pouvez utiliser les paramètres par défaut pour tous les clusters AKS créés dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état d’inscription *Inscrit* s’affiche. Vous pouvez vérifier l’état à l’aide de la commande [az feature list][az-feature-list] suivante :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Une fois que l’état est « Inscrit », actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] suivante :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Créer un cluster AKS privé

### <a name="default-basic-networking"></a>Mise en réseau de base par défaut 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Où *--enable-private-cluster* est un indicateur obligatoire pour un cluster privé. 

### <a name="advanced-networking"></a>Mise en réseau avancée  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Où *--enable-private-cluster* est un indicateur obligatoire pour un cluster privé. 

> [!NOTE]
> Si le CIDR d’adresse de pont Docker (172.17.0.1/16) est en conflit avec le CIDR de sous-réseau, changez l’adresse de pont Docker en conséquence.

## <a name="connect-to-the-private-cluster"></a>Établir une connexion au cluster privé

Le point de terminaison du serveur d’API n’a pas d’adresse IP publique. Par conséquent, vous devez créer une machine virtuelle Azure dans un réseau virtuel et établir une connexion au serveur d’API. Pour ce faire, procédez comme suit :

1. Obtenez les informations d’identification pour vous connecter au cluster.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Effectuez l'une des opérations suivantes :
   * Créez une machine virtuelle dans le même réseau virtuel que le cluster AKS.  
   * Créez une machine virtuelle dans un autre réseau virtuel et appairez ce réseau virtuel avec le réseau virtuel du cluster AKS.

     Si vous créez une machine virtuelle dans un autre réseau virtuel, configurez un lien entre ce réseau virtuel et la zone DNS privée. Pour ce faire :
    
     a. Accédez au groupe de ressources MC_* dans le portail Azure.  
     b. Sélectionnez la zone DNS privée.   
     c. Dans le volet de gauche, sélectionnez le lien **Réseau virtuel**.  
     d. Créez un lien permettant d’ajouter le réseau virtuel de la machine virtuelle à la zone DNS privée. Il faut quelques minutes pour que le lien de zone DNS soit disponible.  
     e. Revenez au groupe de ressources MC_* dans le portail Azure.  
     f. Dans le volet de droite, sélectionnez le réseau virtuel. Le nom du réseau virtuel se présente au format *aks-vnet-\** .  
     g. Dans le volet de gauche, sélectionnez **Appairages**.  
     h. Sélectionnez **Ajouter**, ajoutez le réseau virtuel de la machine virtuelle, puis créez l’appairage.  
     i. Accédez au réseau virtuel sur lequel se trouve la machine virtuelle, sélectionnez **Appairages**, sélectionnez le réseau virtuel AKS, puis créez l’appairage. Si les plages d’adresses sur le réseau virtuel AKS et le réseau virtuel de la machine virtuelle sont en conflit, l’appairage échoue. Pour plus d’informations, consultez [Appairage de réseaux virtuels][virtual-network-peering].

1. Accédez à la machine virtuelle par le biais de Secure Shell (SSH).
1. Installez l’outil Kubectl, puis exécutez les commandes Kubectl.


## <a name="dependencies"></a>Les dépendances  
* Le service Liaison privée est pris en charge uniquement sur Azure Load Balancer Standard. Azure Load Balancer De base n’est pas pris en charge.  
* Pour utiliser un serveur DNS personnalisé, déployez un serveur AD avec DNS à transférer à cette adresse IP : 168.63.129.16.

## <a name="limitations"></a>Limites 
* Les plages d’adresses IP autorisées ne peuvent pas être appliquées au point de terminaison du serveur d’API privé, elles sont uniquement applicables au serveur d’API public
* Les zones de disponibilité sont actuellement prises en charge pour certaines régions, reportez-vous au début de ce document 
* Les [limitations du service Azure Private Link][private-link-service] s’appliquent aux clusters privés, aux points de terminaison privés Azure et aux points de terminaison de service de réseau virtuel qui ne sont actuellement pas pris en charge dans le même réseau virtuel.
* Aucune prise en charge des nœuds virtuels dans un cluster privé pour faire tourner des Azure Container Instances privées dans un réseau virtuel Azure privé
* Aucune prise en charge de l’intégration d’Azure DevOps avec les clusters privés.
* Pour les clients qui doivent activer Azure Container Registry afin d’utiliser des clusters AKS privés, le réseau virtuel Container Registry doit être appairé avec le réseau virtuel du cluster d’agent.
* Aucune prise en charge de Azure Dev Spaces
* Aucune prise en charge de la conversion de clusters AKS existants en clusters privés
* La suppression ou la modification du point de terminaison privé dans le sous-réseau du client entraîne l’arrêt du fonctionnement du cluster. 
* Les données actives Azure Monitor pour conteneurs ne sont actuellement pas prises en charge.
* L’*apport de votre propre DNS* n’est actuellement pas pris en charge.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

