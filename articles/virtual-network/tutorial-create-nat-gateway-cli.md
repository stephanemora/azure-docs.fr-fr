---
title: 'Tutoriel : Créer une passerelle NAT - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Découvrez comment créer une passerelle NAT avec l’interface Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102636743"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Tutoriel : Créer une passerelle NAT avec l’interface Azure CLI

Ce tutoriel vous montre comment utiliser le service NAT de réseau virtuel Azure. Vous allez créer une passerelle NAT pour fournir une connectivité sortante à une machine virtuelle dans Azure. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un réseau virtuel.
> * Création d’une machine virtuelle
> * Créer une passerelle NAT et l’associer au réseau virtuel.
> * Vous connecter à la machine virtuelle et vérifier l’adresse IP NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ce guide de démarrage rapide nécessite la version 2.0.28 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé **myResourceGroupNAT** à l’emplacement **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Créer la passerelle NAT

Dans cette section, nous créons la passerelle NAT et les ressources de prise en charge.

### <a name="create-public-ip-address"></a>Création d’une adresse IP publique

Pour accéder à l’Internet, vous avez besoin d’une ou de plusieurs adresses IP publiques pour la passerelle NAT. Utilisez la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une ressource d’adresse IP publique nommée **myPublicIP** dans **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Créer une ressource de passerelle NAT

Créez une passerelle NAT Azure globale avec la commande [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create). Cette commande aboutit à la création d’une ressource de passerelle nommée **myNATgateway** qui utilise l’adresse IP publique **myPublicIP**. Le délai d’inactivité est défini sur 10 minutes.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

Créez un réseau virtuel nommé **myVnet** avec un sous-réseau nommé **mySubnet** ([az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)) dans le groupe de ressources **myResourceGroup**. L’espace d’adressage IP pour le réseau virtuel est **10.1.0.0/16**. Le sous-réseau au sein du réseau virtuel est **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Créer un hôte bastion

Créez un hôte Azure Bastion nommé **myBastionHost** pour accéder à la machine virtuelle. 

Utilisez [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) pour créer un sous-réseau Azure Bastion.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Créez une adresse IP publique pour l’hôte bastion avec [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Utilisez [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) pour créer l’hôte bastion. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurer le service NAT pour le sous-réseau source

Nous allons configurer le sous-réseau source **mySubnet** dans le réseau virtuel **myVnet** pour utiliser une ressource de passerelle NAT spécifique **myNATgateway** avec la commande [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Cette commande active le service NAT sur le sous-réseau précisé.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Tout le trafic sortant vers les destinations Internet utilise désormais la passerelle NAT.  Il n’est pas nécessaire de configurer un UDR.


## <a name="virtual-machine"></a>Machine virtuelle

Dans cette section, vous allez créer une machine virtuelle pour tester la passerelle NAT afin de vérifier l’adresse IP publique de la connexion sortante.

Créez la machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Attendez la fin de la création de la machine virtuelle avant de passer à la section suivante.

## <a name="test-nat-gateway"></a>Tester la passerelle NAT

Dans cette section, nous allons tester la passerelle NAT. Nous allons d’abord découvrir l’IP publique de la passerelle NAT. Nous allons ensuite nous connecter à la machine virtuelle de test et vérifier la connexion sortante via la passerelle NAT.
    
1. Connectez-vous au [portail Azure](https://portal.azure.com)

1. Recherchez l’adresse IP publique de la passerelle NAT sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez **myPublicIP**.

2. Prenez note de l’adresse IP publique :

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Découvrir l’adresse IP publique de la passerelle NAT" border="true":::

3. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis dans la liste de ressources, sélectionnez **myVM**, qui se trouve dans le groupe de ressources **myResourceGroupNAT**.

4. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

5. Sélectionnez le bouton bleu **Utiliser le bastion**.

6. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

7. Ouvrez **Internet Explorer** sur **myTestVM**.

8. Entrez **https://whatsmyip.com** dans la barre d’adresse.

9. Vérifiez que l’adresse IP affichée correspond à l’adresse de la passerelle NAT que vous avez notée à l’étape précédente :

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer affichant une IP sortante externe" border="true":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le réseau virtuel, la machine virtuelle et la passerelle NAT en effectuant les étapes suivantes :

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le NAT de réseau virtuel Azure, consultez :
> [!div class="nextstepaction"]
> [Vue d’ensemble de NAT de réseau virtuel](nat-overview.md)
