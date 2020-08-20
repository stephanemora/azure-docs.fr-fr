---
title: 'Créez une passerelle VPN Azure basée sur des routes : Interface de ligne de commande'
description: Créez rapidement une passerelle VPN Azure basée sur des itinéraires à l’aide d’Azure CLI, pour une connexion VPN à un réseau local ou pour connecter des réseaux virtuels.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 81f451ace7ec3f032dc7752829d12a0a3598811b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036111"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Créer une passerelle VPN basée sur des itinéraires à l’aide de l’interface CLI

Cet article vous aidera à créer rapidement une passerelle VPN Azure basée sur des itinéraires à l’aide d’Azure CLI. Une passerelle VPN permet de créer une connexion VPN à un réseau local. Vous pouvez également vous en servir pour connecter des réseaux virtuels.

Les étapes décrites dans cet article permettent de créer un réseau virtuel, un sous-réseau, un sous-réseau de passerelle et une passerelle VPN basée sur des itinéraires (passerelle de réseau virtuel). La création de la passerelle de réseau virtuel peut prendre 45 minutes, voire plus. Vous pourrez ensuite créer des connexions. Ces étapes nécessitent un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour trouver la version installée, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Créer un réseau virtuel

Utilisez la commande [az network vnet create](/cli/azure/network/vnet) pour créer un réseau virtuel. L’exemple suivant crée un réseau virtuel nommé **VNet1** à l’emplacement **EastUS** :

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Ajouter un sous-réseau de passerelle

Le sous-réseau de passerelle contient les adresses IP réservées utilisées par les services de passerelle de réseau virtuel. Appuyez-vous sur les exemples suivants pour ajouter un sous-réseau de passerelle :

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Demander une adresse IP publique

Une passerelle VPN doit présenter une adresse IP publique allouée dynamiquement. L’adresse IP publique sera affectée à la passerelle VPN que vous créez pour votre réseau virtuel. Appuyez-vous sur l’exemple suivant pour demander une adresse IP publique :

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Créer la passerelle VPN

Créez la passerelle VPN à l’aide de la commande [az network vnet-gateway create](/cli/azure/group).

Si vous exécutez cette commande à l’aide du paramètre `--no-wait`, vous ne voyez aucun commentaire ni sortie. Le paramètre `--no-wait` permet à la passerelle d’être créée à l’arrière-plan. Cela ne signifie pas que la passerelle VPN est immédiatement créée.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

La création de la passerelle VPN peut prendre 45 minutes, voire plus.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Afficher la passerelle VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

La réponse se présente ainsi :

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Afficher l’adresse IP publique

Pour voir l’adresse IP publique affectée à votre passerelle, utilisez l’exemple suivant :

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

La valeur associée au champ **ipAddress** est l’adresse IP publique de votre passerelle VPN.

Exemple de réponse :

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin des ressources créées, utilisez la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources. Ce faisant, vous supprimez le groupe de ressources et l’ensemble des ressources qu’il contient.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Étapes suivantes

Une fois la création de la passerelle terminée, vous pouvez établir une connexion entre votre réseau virtuel et un autre réseau virtuel. Sinon, créez une connexion entre votre réseau virtuel et un emplacement local.

> [!div class="nextstepaction"]
> [Créer une connexion de site à site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Créer une connexion de point à site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Créer une connexion à un autre réseau virtuel](vpn-gateway-vnet-vnet-rm-ps.md)
