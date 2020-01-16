---
title: Cluster Azure Kubernetes Service privé
description: Découvrez comment créer un cluster Azure Kubernetes Service (AKS) privé
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475015"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Version préliminaire publique - Cluster Azure Kubernetes Service privé

Dans un cluster privé, le plan de contrôle/serveur d’API aura des adresses IP internes définies dans [RFC1918](https://tools.ietf.org/html/rfc1918).  En utilisant un cluster privé, vous pouvez vous assurer que le trafic réseau entre votre serveur d’API et vos pools de nœuds reste sur le réseau privé uniquement.

Le plan de contrôle/serveur d’API, qui se trouve dans un abonnement Azure géré par AKS, et le pool de clusters/nœuds des clients, qui se trouve dans un abonnement client, peuvent communiquer entre eux via le [service de liaison privée][private-link-service] dans le réseau virtuel du serveur d’API et un point de terminaison privé exposé dans le sous-réseau du cluster AKS

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS](support-policies.md)
> * [FAQ du support Azure](faq.md)

## <a name="before-you-begin"></a>Avant de commencer

* Vous avez besoin de la version Azure CLI 2.0.77 ou ultérieure et de l’extension AKS-preview 0.4.18

## <a name="current-supported-regions"></a>Régions prises en charge actuellement
* USA Ouest
* USA Ouest 2
* USA Est 2
* Centre du Canada
* Europe Nord
* Europe Ouest
* Australie Est

## <a name="install-latest-aks-cli-preview-extension"></a>Installer la dernière extension de la préversion d’AKS CLI

Pour utiliser des clusters privés, vous devez disposer de l’extension *aks-preview* version 0.4.18 ou ultérieure. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités en préversion, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état s’affiche *Inscrit*. Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Une fois l’état inscrit, actualisez l’inscription du fournisseur de ressources de *Microsoft. ContainerService* à l’aide de la commande [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Créer un cluster AKS privé

#### <a name="default-basic-networking"></a>Mise en réseau de base par défaut 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Où – Enable-Private-cluster est un indicateur obligatoire pour un cluster privé 

#### <a name="advanced-networking"></a>Mise en réseau avancée  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
Où – Enable-Private-cluster est un indicateur obligatoire pour un cluster privé 

## <a name="steps-to-connect-to-the-private-cluster"></a>Procédure de connexion au cluster privé
Le point de terminaison du serveur d’API n’a pas d’adresse IP publique. Par conséquent, les utilisateurs doivent créer une machine virtuelle Azure dans un réseau virtuel et se connecter au serveur d’API. Les étapes décrites dans

* Obtenez les informations d’identification pour vous connecter au cluster

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Créez une machine virtuelle dans le même réseau virtuel que le cluster AKS, ou créez une machine virtuelle dans un autre réseau virtuel et homologuez ce réseau virtuel avec le réseau virtuel de cluster AKS
* Si vous créez une machine virtuelle dans un autre réseau virtuel, vous devez configurer un lien entre ce réseau virtuel et la zone de DNS privé
    * Accédez au groupe de ressources MC_ * dans le portail. 
    * Cliquez sur la zone DNS privé 
    * sélectionnez le lien réseau virtuel dans le volet gauche
    * créer un lien pour ajouter le réseau virtuel de la machine virtuelle à la zone DNS privé *(quelques minutes sont nécessaires pour que le lien de la zone DNS devienne disponible)*
* Connectez-vous avec SSH à la machine virtuelle
* Installez l’outil Kubectl et exécutez des commandes Kubectl

## <a name="dependencies"></a>Les dépendances  
* Équilibreur de charge standard uniquement – Aucune prise en charge pour l’équilibrage de charge de base  

## <a name="limitations"></a>Limites 
* Les mêmes [limitations du service de liaison privée Azure][private-link-service] s’appliquent aux clusters privés, aux points de terminaison privés Azure et aux points de terminaison de service de réseau virtuel qui ne sont pas actuellement pris en charge dans le même réseau virtuel
* Aucune prise en charge des nœuds virtuels dans un cluster privé pour faire tourner des instances ACI privées dans un réseau virtuel Azure privé
* Aucune prise en charge de l’intégration d’Azure DevOps avec les clusters privés.
* Si les clients doivent activer ACR pour qu’ils fonctionnent avec des AKS privées, le réseau virtuel de ACR devra être homologué avec le réseau virtuel de cluster d’agent.
* Aucune prise en charge de Azure Dev Spaces
* Aucune prise en charge pour convertir les clusters AKS existants en clusters privés  
* La suppression ou la modification du point de terminaison privé dans le sous-réseau du client entraîne l’arrêt du fonctionnement du cluster 
* Les données actives de Azure Monitor pour les conteneurs ne sont pas prises en charge actuellement
* L’apport de votre propre DNS n’est pas pris en charge actuellement


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
