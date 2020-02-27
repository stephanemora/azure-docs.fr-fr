---
title: 'Démarrage rapide : Créer une passerelle NAT - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Ce guide de démarrage rapide montre comment créer une passerelle NAT à l’aide de l’interface Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: df1e363f31aa8c88be54454c9dc060f4ed6b7ca1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588856"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Démarrage rapide : Créer une passerelle NAT avec Azure CLI

Ce guide de démarrage rapide vous montre comment utiliser le service NAT de Réseau virtuel Azure. Vous allez créer une passerelle NAT pour fournir une connectivité sortante à une machine virtuelle dans Azure. 

>[!NOTE] 
>Le service NAT de Réseau virtuel Azure est disponible en préversion publique à ce stade, et disponible dans un ensemble limité de [régions](https://azure.microsoft.com/global-infrastructure/regions/). Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vous pouvez suivre ce tutoriel en utilisant Azure Cloud Shell ou exécuter les commandes respectives localement.  Si vous n’avez jamais utilisé Azure Cloud Shell, [connectez-vous maintenant](https://shell.azure.com) pour procéder à l’installation initiale.
Si vous choisissez d’exécuter ces commandes localement, vous devez installer l’interface CLI.  Ce tutoriel nécessite l’exécution d’une instance d’Azure CLI version 2.0.71 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé **myResourceGroupNAT** à l’emplacement **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Créer la passerelle NAT

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à l’Internet public, vous avez besoin d’une ou de plusieurs adresses IP publiques pour la passerelle NAT. Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) pour créer une ressource d’adresse IP publique nommée **myPublicIP** dans **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Créer un préfixe d’adresse IP publique

Vous pouvez utiliser une ou plusieurs ressources d’adresse IP publique, des préfixes d’adresse IP publique, ou ces deux options à la fois avec la passerelle NAT. Nous allons ajouter une ressource de préfixe d’adresse IP publique à ce scénario pour le démontrer.   Utilisez la commande [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) pour créer une ressource de préfixe d’adresse IP publique nommée **myPublicIPPrefix** dans **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Créer une ressource de passerelle NAT

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants du service NAT à l’aide de la ressource de passerelle NAT :
  - Un pool d’adresses IP publiques et un préfixe d’adresse IP publique à utiliser pour les flux sortants qui sont traduits par la ressource de passerelle NAT.
  - Passer la valeur par défaut du délai d’inactivité de 4 minutes à 10 minutes.

Créez une passerelle NAT Azure globale avec la commande [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) nommée **myNATgateway**. La commande utilise à la fois l’adresse IP publique **myPublicIP** et le préfixe d’adresse IP publique **myPublicIPprefix**. La commande passe le délai d’inactivité à **10** minutes.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

À ce stade, la passerelle NAT est opérationnelle et il ne reste plus qu’à définir quels sous-réseaux d’un réseau virtuel doivent l’utiliser.

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Avant de déployer une machine virtuelle et de pouvoir utiliser votre passerelle NAT, il nous faut créer le réseau virtuel.

Créez un réseau virtuel nommé **myVnet** avec un sous-réseau nommé **mySubnet** dans **myResourceGroupNAT** à l’aide de la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).  L’espace d’adressage IP pour le réseau virtuel est **192.168.0.0/16**. Le sous-réseau au sein du réseau virtuel est **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurer le service NAT pour le sous-réseau source

Nous allons configurer le sous-réseau source **mySubnet** dans le réseau virtuel **myVnet** pour utiliser une ressource de passerelle NAT spécifique **myNATgateway** avec la commande [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Cette commande active le service NAT sur le sous-réseau précisé.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Tout le trafic sortant vers les destinations Internet utilise désormais la passerelle NAT.  Il n’est pas nécessaire de configurer un UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Créer une machine virtuelle pour utiliser le service NAT

Nous allons à présent créer une machine virtuelle pour utiliser le service NAT.  Cette machine virtuelle dispose d’une adresse IP publique à utiliser comme adresse IP publique au niveau de l’instance pour vous permettre d’accéder à la machine virtuelle.  Le service NAT reconnaît la direction du flux et remplace la destination Internet par défaut dans votre sous-réseau. L’adresse IP publique de la machine virtuelle n’est pas utilisée pour les connexions sortantes.

### <a name="create-public-ip-for-source-vm"></a>Créer une adresse IP publique pour la machine virtuelle source

Nous créons une adresse IP publique à utiliser pour accéder à la machine virtuelle.  Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) pour créer une ressource d’adresse IP publique nommée **myPublicIPVM** dans **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Créer un groupe de sécurité réseau pour une machine virtuelle

Les adresses IP publiques standard étant « sécurisées par défaut », nous devons créer un groupe de sécurité réseau pour autoriser SSH à l’accès entrant. Utilisez [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) pour créer une ressource de groupe de sécurité réseau nommée **myNSG** dans **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Exposer le point de terminaison SSH sur la machine virtuelle source

Nous créons une règle dans le groupe de sécurité réseau pour l’accès SSH à la machine virtuelle source. Utilisez la commande [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) pour créer une règle de groupe de sécurité réseau nommée **ssh** dans le groupe de sécurité réseau **myNSG** de **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Créer une carte réseau pour une machine virtuelle

Créez une interface réseau à l’aide de la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) et associez-la à l’adresse IP publique et au groupe de sécurité réseau. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Créer une machine virtuelle

Créez la machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create).  Nous générons des clés SSH pour cette machine virtuelle, et stockons la clé privée à utiliser ultérieurement.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Attendez la fin du déploiement de la machine virtuelle, puis passez aux étapes restantes.

## <a name="discover-the-ip-address-of-the-vm"></a>Découvrir l’adresse IP de la machine virtuelle

Tout d’abord, nous avons besoin de découvrir l’adresse IP de la machine virtuelle que vous avez créée. Pour récupérer l’adresse IP publique de la machine virtuelle, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copiez l’adresse IP publique, puis collez-la dans un bloc-notes afin de pouvoir l’utiliser pour accéder à la machine virtuelle.

### <a name="sign-in-to-vm"></a>Se connecter à la machine virtuelle

Les informations d’identification SSH doivent être stockées dans votre instance Cloud Shell depuis l’opération précédente.  Ouvrez une session [Azure Cloud Shell](https://shell.azure.com) dans votre navigateur. Utilisez l’adresse IP récupérée à l’étape précédente pour établir une connexion SSH à la machine virtuelle.

```bash
ssh <ip-address-destination>
```

Vous êtes désormais prêt à utiliser le service NAT.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, utilisez la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources et toutes les ressources qu’il contient.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une passerelle NAT et une machine virtuelle pour l’utiliser. 

Consultez les métriques dans Azure Monitor pour découvrir le fonctionnement de votre service NAT. Diagnostiquez des problèmes, tels que l’épuisement des ressources des ports SNAT disponibles.  L’épuisement des ressources de ports SNAT est résolu en ajoutant des ressources supplémentaires, d’adresse IP publique ou de préfixe d’adresse IP publique, ou des deux à la fois.


- Apprenez-en davantage sur le service [NAT de Réseau virtuel Azure](./nat-overview.md).
- Apprenez-en davantage sur la [ressource de passerelle NAT](./nat-gateway-resource.md).
- Guide de démarrage rapide du déploiement d’une [ressource de passerelle NAT avec Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Guide de démarrage rapide du déploiement d’une [ressource de passerelle NAT avec Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Guide de démarrage rapide du déploiement d’une [ressource de passerelle NAT avec le portail Azure](./quickstart-create-nat-gateway-portal.md).
- [Faites-nous part de vos commentaires sur la préversion publique](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

