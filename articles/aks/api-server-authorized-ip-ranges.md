---
title: Serveur d’API autorisé de plages d’adresses IP dans Azure Kubernetes Service (ACS)
description: Découvrez comment à sécuriser votre cluster à l’aide d’une adresse IP de plages d’adresses pour l’accès au serveur d’API dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 185c16e76094fe55a54fb17bef24fcd03d7b54f0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475145"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Preview - le serveur d’API à l’aide d’un accès sécurisé autorisé des plages d’adresses IP dans Azure Kubernetes Service (ACS)

Dans Kubernetes, le serveur d’API reçoit des requêtes pour effectuer des actions dans le cluster comme pour créer des ressources ou de mettre à l’échelle le nombre de nœuds. Le serveur de l’API est le moyen centralisé pour interagir avec et gérer un cluster. Pour améliorer la sécurité du cluster et minimiser les attaques, le serveur d’API doit uniquement être accessible à partir d’un ensemble limité de plages d’adresses IP.

Cet article vous montre comment utiliser des plages d’adresses IP API serveur autorisé pour limiter les demandes à un plan de contrôle. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont en libre-service, participer. Elles sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Dans la version préliminaire, ces fonctionnalités ne sont pas destinées à des fins de production. Fonctionnalités en version préliminaire publique relèvent du « meilleur effort » la prise en charge. L’assistance des équipes de support technique AKS est disponible pendant les heures de bureau PST fuseau horaire (PST) uniquement. Pour plus d’informations, consultez les éléments suivants prennent en charge des articles :
>
> * [Stratégies de prise en charge AKS][aks-support-policies]
> * [FAQ du Support Azure][aks-faq]

## <a name="before-you-begin"></a>Avant de commencer

Serveur d’API autorisé plages d’adresses IP ne fonctionnent que pour les nouveaux clusters ACS que vous créez. Cet article vous montre comment créer un cluster AKS à l’aide de l’interface CLI.

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Les commandes CLI pour configurer les plages IP de serveur autorisé API sont disponibles dans le *aks-preview* extension CLI. Installer le *aks-preview* extension Azure CLI à l’aide du [az extension ajoutez] [ az-extension-add] commande, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Si vous avez installé précédemment le *aks-preview* extension, installez toutes des mises à jour à l’aide de la `az extension update --name aks-preview` commande.

### <a name="register-feature-flag-for-your-subscription"></a>Inscrire l’indicateur de fonctionnalité pour votre abonnement

Pour utiliser l’API serveur autorisé de plages d’adresses IP, commencez par activer un indicateur de fonctionnalité sur votre abonnement. Pour inscrire le *APIServerSecurityPreview* indicateur des fonctionnalités, utilisez le [register de fonctionnalité az] [ az-feature-register] commande comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'enregistrement à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous configurez les plages d’adresses IP API serveur autorisé :

* Vous ne pouvez pas utiliser actuellement de Azure Dev espaces comme la communication avec le serveur d’API est également bloquée.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Vue d’ensemble du serveur API autorisé de plages d’adresses IP

Le serveur d’API Kubernetes est comment APIs Kubernetes sous-jacentes sont exposés. Ce composant fournit l’interaction des outils de gestion, tels que `kubectl` ou le tableau de bord Kubernetes. AKS fournit un maître de cluster de client unique, avec un serveur d’API dédié. Par défaut, le serveur d’API est attribué une adresse IP publique, et vous devez contrôler l’accès à l’aide de contrôles d’accès en fonction du rôle (RBAC).

Pour sécuriser l’accès au plan de contrôle AKS sinon accessible publiquement / serveur d’API, vous pouvez activer et utiliser autorisé de plages d’adresses IP. Ces plages d’adresses IP autorisées autorisent uniquement les plages d’adresses IP définies communiquer avec le serveur d’API. Une demande adressée au serveur d’API à partir d’une adresse IP qui ne fait pas partie de ces plages d’adresses IP autorisées est bloquée. Vous devez continuer à utiliser RBAC pour autoriser des utilisateurs et les actions qu’ils demandent.

Pour plus d’informations sur le serveur d’API et d’autres composants de cluster, consultez [Kubernetes principaux concepts pour AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Les plages d’adresse IP du serveur autorisé API fonctionnent uniquement pour les nouveaux clusters AKS. Vous ne pouvez pas activer plages d’adresses IP autorisées comme opération de création de la partie du cluster. Si vous essayez d’activer les plages IP autorisées en tant que partie du cluster de créer des processus, les nœuds de cluster ne parvenez pas à accéder au serveur de API pendant le déploiement comme l’adresse IP de sortie n’est pas défini à ce stade.

Commencez par créer un cluster à l’aide de la [créer az aks] [ az-aks-create] commande. L’exemple suivant crée un cluster à nœud unique nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*.

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>Créer une passerelle sortante pour les règles de pare-feu

Pour vous assurer que les nœuds dans un cluster peuvent communiquer de manière fiable avec le serveur d’API lorsque vous activez des plages d’adresses IP autorisées dans la section suivante, créer un pare-feu Azure pour une utilisation en tant que la passerelle sortante. L’adresse IP du pare-feu Azure est ensuite ajoutée à la liste des adresses IP de serveur API autorisés dans la section suivante.

> [!WARNING]
> L’utilisation du pare-feu d’Azure peut entraîner une baisse des coûts importants un cycle de facturation mensuel. La nécessité d’utiliser des pare-feu Azure doit uniquement être nécessaire dans cette période d’évaluation initiale. Pour plus d’informations et planification du coût, consultez [pare-feu Azure tarification][azure-firewall-costs].

Tout d’abord, obtenez le *MC_* nom de groupe de ressources pour le cluster AKS et le réseau virtuel. Ensuite, créez un sous-réseau à l’aide de la [créer de sous-réseau de réseau virtuel du réseau az] [ az-network-vnet-subnet-create] commande. L’exemple suivant crée un sous-réseau nommé *AzureFirewallSubnet* avec la plage CIDR du *10.200.0.0/16*:

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

Pour créer un pare-feu d’Azure, installez le *azure-pare-feu* extension CLI à l’aide la [az extension ajoutez] [ az-extension-add] commande. Ensuite, créez un pare-feu à l’aide de la [créer de pare-feu de réseau az] [ az-network-firewall-create] commande. L’exemple suivant crée un pare-feu Azure nommé *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Un pare-feu d’Azure est affecté une adresse IP publique qui parcourt de trafic sortant. Créer une adresse publique à l’aide de la [créer az network public-ip] [ az-network-public-ip-create] de commandes, puis créez une configuration IP sur le pare-feu à l’aide la [az réseau pare-feu ip-config créer] [ az-network-firewall-ip-config-create] qui s’applique à l’adresse IP publique :

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

Maintenant créer la règle de réseau de pare-feu Azure pour *autoriser* tous les *TCP* du trafic à l’aide de la [créer az réseau-règle de pare-feu réseau] [ az-network-firewall-network-rule-create] commande. L’exemple suivant crée une règle réseau nommée *AllowTCPOutbound* pour le trafic avec n’importe quelle adresse source ou de destination :

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

Pour associer le pare-feu Azure à l’itinéraire du réseau, d’obtenir les informations de table de routage existante, l’adresse IP interne du pare-feu Azure, puis l’adresse IP du serveur API. Ces adresses IP sont spécifiées dans la section suivante pour contrôler la façon dont le trafic doit être routé pour la communication de cluster.

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

Enfin, créez un itinéraire dans existant AKS réseau route table en utilisant le [créer itinéraire de table de routage réseau az] [ az-network-route-table-route-create] commande qui autorise le trafic à utiliser l’appliance de pare-feu Azure pour serveur d’API communication.

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

Prenez note de l’adresse IP publique de votre appliance de pare-feu Azure. Cette adresse est ajoutée à la liste des plages d’IP API serveur autorisé dans la section suivante.

## <a name="enable-authorized-ip-ranges"></a>Activer les plages d’adresses IP autorisées

Pour activer les plages d’adresses IP API serveur autorisé, vous fournissez une liste de plages d’adresses IP autorisées. Lorsque vous spécifiez une plage CIDR, démarrez avec la première adresse IP dans la plage. Par exemple, *137.117.106.90/29* est une plage valide, mais assurez-vous que vous spécifiez la première adresse IP dans la plage, comme *137.117.106.88/29*.

Utilisez [az aks mettre à jour] [ az-aks-update] commande et spécifiez le *--api-server-autorisé--plages d’adresses ip* à autoriser. Ces plages d’adresses IP sont généralement des plages d’adresses utilisées par vos réseaux locaux. Ajouter l’adresse IP publique de votre propre pare-feu Azure obtenu à l’étape précédente, tel que *20.42.25.196/32*.

L’exemple suivant active les plages d’IP API serveur autorisé sur le cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*. Les plages d’adresses IP pour autoriser sont *20.42.25.196/32* (le pare-feu Azure adresse IP publique), puis *172.0.0.10/16* et *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Mettre à jour ou désactiver des plages d’adresses IP autorisées

Pour mettre à jour ou désactiver des plages d’adresses IP autorisées, vous utilisez à nouveau [az aks mettre à jour] [ az-aks-update] commande. Spécifiez la plage CIDR mis à jour que vous souhaitez autoriser, ou spécifiez une plage vide pour désactiver le serveur d’API autorisé de plages d’adresses IP, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez activé les plages d’adresses IP API serveur autorisé. Cette approche est une partie de la façon dont vous pouvez exécuter un cluster AKS sécurisé.

Pour plus d’informations, consultez [les concepts de sécurité pour les applications et des clusters dans AKS] [ concepts-security] et [meilleures pratiques pour la sécurité du cluster et les mises à niveau dans AKS] [ operator-best-practices-cluster-security].

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
