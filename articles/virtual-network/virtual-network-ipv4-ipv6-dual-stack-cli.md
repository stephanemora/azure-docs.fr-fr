---
title: Déployer une application de double pile IPv6 dans un réseau virtuel Azure - CLI
titlesuffix: Azure Virtual Network
description: Cet article montre comment déployer une application de double pile IPv6 dans un réseau virtuel Azure à l’aide d’Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130920"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Déployer une application de double pile IPv6 dans un réseau virtuel Azure - CLI (version préliminaire)

Cet article vous montre comment déployer une application de double pile (IPv4 + IPv6) dans Azure, qui inclut un réseau virtuel de pile double avec un sous-réseau de pile double, un équilibreur de charge avec les configurations frontales double (IPv4 + IPv6), les machines virtuelles avec des cartes réseau qui ont une configuration IP double, règles de groupe de sécurité réseau double et deux adresses IP publiques.

> [!Important]
> IPv6 double pile de réseau virtuel Azure est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI localement au lieu de cela, ce démarrage rapide, vous devez utiliser Azure CLI version 2.0.49 ou version ultérieure. Exécutez `az --version` pour rechercher la version installée. Pour des informations d'installation ou de mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser l’adresse IPv6 pour la fonctionnalité de réseau virtuel Azure, vous devez configurer votre abonnement à l’aide d’Azure PowerShell comme suit :

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Vous pouvez vérifier votre statut d’enregistrement en exécutant la commande Azure CLI suivante :

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
À l’issue de l’installation, exécutez la commande suivante :

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer votre réseau virtuel double pile, vous devez créer un groupe de ressources avec [az groupe créer](/cli/azure/group). L’exemple suivant crée un groupe de ressources nommé *myRGDualStack* dans le *eastus* emplacement :

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Créer des adresses IPv4 et IPv6 publiques IP pour l’équilibrage de charge
Pour accéder à vos points de terminaison IPv4 et IPv6 sur Internet, vous avez besoin d’adresses IP publiques IPv4 et IPv6 pour l’équilibrage de charge. Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip). L’exemple suivant crée IPv4 et IPv6 adresse IP publique nommée *dsPublicIP_v4* et *dsPublicIP_v6* dans le *myRGDualStack* groupe de ressources :

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Créer des adresses IP publiques pour les machines virtuelles

Pour accéder à distance à vos machines virtuelles sur internet, vous avez besoin d’adresses IP publiques IPv4 pour les machines virtuelles. Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

Dans cette section, vous configurez double frontend IP (IPv4 et IPv6) et le pool d’adresses back-end pour l’équilibrage de charge, puis créez un équilibreur de charge de base.

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Créer l’équilibreur de charge de base avec [créer az network lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nommé **dsLB** qui inclut un pool frontal nommé **dsLbFrontEnd_v4**, un pool principal nommé  **dsLbBackEndPool_v4** qui est associé à l’adresse IP IPv4 **dsPublicIP_v4** que vous avez créé à l’étape précédente. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Créer le serveur frontal IPv6

Créer une adresse IP frontale de IPV6 avec [créer az network lb frontend-ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). L’exemple suivant crée une configuration IP de serveur frontal nommée *dsLbFrontEnd_v6* et attache le *dsPublicIP_v6* adresse :

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurer le pool d’adresses back-end de IPv6

Créer des pools avec une adresse de serveur principal IPv6 [créer az network lb-pool d’adresses](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). L’exemple suivant crée le pool d’adresses back-end nommé *dsLbBackEndPool_v6* à inclure des machines virtuelles avec les configurations de carte réseau IPv6 :

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. 

Utilisez [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) pour créer une règle d’équilibrage de charge. L’exemple suivant crée des règles d’équilibrage de charge nommés *dsLBrule_v4* et *dsLBrule_v6* et équilibre le trafic sur *TCP* port *80* à configurations IP frontales IPv4 et IPv6 :

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Créer des ressources réseau
Avant de déployer des machines virtuelles, vous devez créer les ressources réseau prise en charge - haute disponibilité, de groupe de sécurité réseau, de réseau virtuel et de cartes réseau virtuelles. 
### <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité
Pour améliorer la disponibilité de votre application, placez vos machines virtuelles dans un groupe à haute disponibilité.

Créez un groupe à haute disponibilité avec la commande [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). L’exemple suivant crée un jeu nommée de disponibilité *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau

Créer un groupe de sécurité réseau pour les règles qui gouvernent les communications entrantes et sortantes de votre réseau virtuel.

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau avec [créer az réseau](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Créer une règle de groupe de sécurité réseau pour les connexions entrantes et sortantes

Créer une règle de groupe de sécurité réseau pour autoriser les connexions RDP via une connexion internet au port 3389, via le port 80 et pour les connexions sortantes avec [créer règle de groupe de sécurité réseau du réseau az](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). L’exemple suivant crée un réseau virtuel nommé *dsVNET* avec sous-réseaux *dsSubNET_v4* et *dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Créer des cartes réseau

Créer des cartes réseau virtuelles pour chaque machine virtuelle avec [az réseau nic créer](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). L’exemple suivant crée une carte réseau virtuelle pour chaque machine virtuelle. Chaque carte réseau a deux configurations IP (1 configuration IPv4, 1 configuration IPv6). Vous créez la configuration IPV6 avec [créer az network nic ip-config](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez les machines virtuelles avec [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). L’exemple suivant crée deux machines virtuelles ainsi que les composants de réseau virtuel nécessaires s’ils n’existent pas encore. 

Créer la machine virtuelle *dsVM0* comme suit :

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Créer la machine virtuelle *dsVM1* comme suit :

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Réseau virtuel de mode IPv6 double pile dans le portail Azure
Vous pouvez afficher le réseau virtuel de double pile IPv6 dans le portail Azure comme suit :
1. Dans la barre de recherche du portail, entrez *dsVnet*.
2. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la. Cette opération lance le **vue d’ensemble** page du réseau virtuel double pile nommé *dsVnet*. Le réseau virtuel de pile double montre les deux cartes réseau avec des configurations à la fois IPv4 et IPv6 situées dans le sous-réseau de pile double nommé *dsSubnet*.

  ![IPv6 double pile réseau virtuel Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> L’adresse IPv6 pour le réseau virtuel Azure est disponible dans le portail Azure en lecture seule pour cette version préliminaire.


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un équilibreur de charge avec une configuration IP de frontend double (IPv4 et IPv6). Vous avez également créé un deux machines virtuelles incluant des cartes réseau avec deux configurations IP (IPV4 + IPv6) qui ont été ajoutées pour le pool back-end de l’équilibreur de charge. Pour en savoir plus sur la prise en charge de IPv6 dans les réseaux virtuels Azure, consultez [Nouveautés IPv6 pour le réseau virtuel Azure ?](ipv6-overview.md)