---
title: Plages d’adresses IP autorisées par le serveur d’API dans Azure Kubernetes Service (AKS)
description: Apprenez à sécuriser votre cluster à l’aide d’une plage d’adresses IP pour l’accès au serveur d’API dans Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 5049a35b943c68d1a05d1435113226d83dc5ecf4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031759"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Préversion - Sécuriser l’accès au serveur d’API à l’aide de plages d’adresses IP autorisées dans Azure Kubernetes Service (AKS)

Dans Kubernetes, le serveur d’API reçoit des requêtes pour effectuer des actions dans le cluster, comme la création de ressources ou la mise à l’échelle du nombre de nœuds. Le serveur d’API est le moyen centralisé pour interagir avec et gérer un cluster. Pour améliorer la sécurité du cluster et minimiser les attaques, le serveur d’API doit uniquement être accessible à partir d’un ensemble limité de plages d’adresses IP.

Cet article vous montre comment utiliser des plages d’adresses IP autorisées pour le serveur d’API pour limiter les requêtes au plan de contrôle. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en-l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

## <a name="before-you-begin"></a>Avant de commencer

Les plages d’adresses IP autorisées pour le serveur d’API ne fonctionnent que pour les nouveaux clusters AKS que vous créez. Cet article vous montre comment créer un cluster AKS à l’aide d’Azure CLI.

L’interface de ligne de commande Azure (Azure CLI) version 2.0.61 ou une version ultérieure doit avoir été installée et configurée. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Pour configurer des plages d’adresses IP autorisées pour le serveur d’API, vous aurez besoin de l’extension de CLI *aks-preview* version 0.4.1 ou une version ultérieure. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Inscrire l’indicateur de fonctionnalité pour votre abonnement

Pour utiliser des plages d’adresses IP autorisées pour le serveur d’API, commencez par activer un indicateur de fonctionnalité sur votre abonnement. Pour enregistrer l’indicateur de fonctionnalité *APIServerSecurityPreview*, utilisez la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités en préversion, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état de l’enregistrement à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous configurez des plages d’adresses IP autorisées pour le serveur d’API :

* Vous ne pouvez actuellement pas utiliser Azure Dev Spaces, car la communication avec le serveur d’API est également bloquée.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Vue d’ensemble des plages d’adresses IP pour le serveur d’API

Le serveur d’API Kubernetes détermine la façon dont les API Kubernetes sous-jacentes sont exposées. Ce composant fournit l’interaction des outils de gestion, tels que `kubectl` ou le tableau de bord Kubernetes. AKS fournit un maître de cluster monolocataire doté d’un serveur d’API dédié. Par défaut, le serveur d’API reçoit une adresse IP publique, et vous devez contrôler l’accès à l’aide de contrôles d’accès en fonction du rôle (RBAC).

Pour sécuriser l’accès au plan de contrôle AKS/serveur d’API, normalement accessible, vous pouvez activer et utiliser des plages d’adresses IP autorisées. Ces plages d’adresses IP autorisées autorisent uniquement les plages d’adresses IP définies à communiquer avec le serveur d’API. Une demande adressée au serveur d’API depuis une adresse IP qui ne fait pas partie de ces plages d’adresses IP autorisées est bloquée. Vous devez continuer à utiliser RBAC pour autoriser des utilisateurs et les actions qu’ils demandent.

Pour plus d’informations sur le serveur d’API et les autres composants de cluster, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Les plages d’adresses IP autorisées pour le serveur d’API ne fonctionnent que pour les nouveaux clusters AKS. Vous ne pouvez pas activer les plages d’adresses IP autorisées dans le cadre de l’opération de création du cluster. Si vous essayez d’activer les plages IP autorisées dans le cadre du processus de création du cluster, les nœuds de cluster ne peuvent pas à accéder au serveur d’API pendant le déploiement, car l’adresse IP de sortie n’est pas définie à ce stade.

D’abord, créez un cluster avec la commande [az aks create][az-aks-create]. L’exemple suivant crée un cluster à nœud unique nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Créer des règles de passerelle sortante pour le pare-feu

Pour vous assurer que les nœuds dans un cluster peuvent communiquer de manière fiable avec le serveur d’API lorsque vous activez les plages d’adresses IP autorisées dans la section suivante, créez un pare-feu Azure à utiliser en tant que passerelle sortante. L’adresse IP du pare-feu Azure est ensuite ajoutée à la liste des adresses IP autorisées du serveur d’API dans la section suivante.

> [!WARNING]
> L’utilisation du pare-feu Azure peut entraîner des coûts importants par rapport à un cycle de facturation mensuel. L’exigence d’utiliser un pare-feu Azure devrait uniquement être valable lors de cette période de préversion initiale. Pour plus d’informations et la planification des coûts, consultez [Tarification du pare-feu Azure][azure-firewall-costs].

Tout d’abord, obtenez le nom de groupe de ressources *MC_* pour le cluster AKS et le réseau virtuel. Ensuite, créez un sous-réseau en exécutant la commande [azure network vnet subnet create][az-network-vnet-subnet-create]. L’exemple suivant crée un sous-réseau nommé *AzureFirewallSubnet* avec la plage CIDR *10.200.0.0/16* :

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Pour créer un pare-feu Azure, installez l’extension de CLI *azure-firewall* à l’aide de la commande [az extension add][az-extension-add]. Ensuite, créez un pare-feu à l’aide de la commande [az network firewall create][az-network-firewall-create]. L’exemple suivant crée un pare-feu Azure nommé *myAzureFirewall* :

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Un pare-feu Azure reçoit une adresse IP publique qui émet des flux de trafic. Créez une adresse publique à l’aide de la commande [az network public-ip create][az-network-public-ip-create], puis créez une configuration IP sur le pare-feu à l’aide de la commande [az network firewall ip-config create][az-network-firewall-ip-config-create], qui applique l’adresse IP publique :

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Créez maintenant la règle réseau de pare-feu Azure pour *autoriser* tout le trafic *TCP* à l’aide de la commande [az network firewall network-rule create][az-network-firewall-network-rule-create]. L’exemple suivant crée une règle réseau nommée *AllowTCPOutbound* pour le trafic avec n’importe quelle adresse source ou de destination :

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Pour associer le pare-feu Azure à l’itinéraire réseau, obtenez les informations de la table de routage existante, l’adresse IP interne du pare-feu Azure, puis l’adresse IP du serveur API. Ces adresses IP sont spécifiées dans la section suivante pour contrôler la façon dont le trafic doit être acheminé pour la communication du cluster.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Enfin, créez un itinéraire dans une table de routage réseau AKS existante en utilisant la commande [az network route-table route create][az-network-route-table-route-create] qui autorise le trafic à utiliser l’appliance de pare-feu Azure pour la communication avec le serveur d’API.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Prenez note de l’adresse IP publique de votre appliance de pare-feu Azure. Cette adresse est ajoutée à la liste des plages d’adresses IP autorisées pour le serveur d’API dans la section suivante.

## <a name="enable-authorized-ip-ranges"></a>Activer les plages d’adresses IP autorisées

Pour activer des plages d’adresses IP autorisées pour le serveur d’API, vous fournissez une liste de plages d’adresses IP autorisées. Lorsque vous spécifiez une plage CIDR, commencez par la première adresse IP dans la plage. Par exemple, *137.117.106.90/29* est une plage valide, mais assurez-vous que vous spécifiez la première adresse IP dans la plage, par exemple *137.117.106.88/29*.

Utilisez la commande [az aks update][az-aks-update] et spécifiez les plages d’adresses IP *--api-server-authorized-ip-ranges* à autoriser. Ces plages d’adresses IP sont généralement des plages d’adresses utilisées par vos réseaux locaux. Ajoutez l’adresse IP publique de votre propre pare-feu Azure obtenue à l’étape précédente, par exemple *20.42.25.196/32*.

L’exemple suivant active les plages d’adresses IP autorisées pour le serveur d’API sur le cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*. Les plages d’adresses IP à autoriser sont *20.42.25.196/32* (adresse IP publique du pare-feu Azure), puis *172.0.0.0/16* et *168.10.0.0/18* :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Mettre à jour ou désactiver des plages d’adresses IP autorisées

Pour mettre à jour ou désactiver des plages d’adresses IP autorisées, vous utilisez à nouveau la commande [az aks update][az-aks-update]. Spécifiez la plage CIDR mise à jour que vous souhaitez autoriser, ou spécifiez une plage vide pour désactiver les plages d’adresses IP autorisées pour le serveur d’API, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez activé des plages d’adresses IP autorisées pour le serveur d’API. Cette approche est une partie de la façon dont vous pouvez exécuter un cluster AKS sécurisé.

Pour plus d’informations, consultez [Concepts de sécurité pour les applications et les clusters dans AKS][concepts-security] et [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans Azure Kubernetes Service][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
