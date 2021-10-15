---
title: Gérer les stratégies réseau pour les points de terminaison privés
titleSuffix: Azure Private Link
description: Découvrez comment désactiver les stratégies réseau pour les points de terminaison privés.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 08/26/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d4671823f5eb3f186007aea46983860269d916b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361757"
---
# <a name="manage-network-policies-for-private-endpoints"></a>Gérer les stratégies réseau pour les points de terminaison privés

Les stratégies réseau, comme les groupes de sécurité réseau (NSG), ne sont pas prises en charge pour les points de terminaison privés. Pour déployer un point de terminaison privé sur un sous-réseau donné, vous devez configurer un paramètre de désactivation explicite sur ce sous-réseau. Ce paramètre s’applique seulement aux points de terminaison privés. Pour les autres ressources du sous-réseau, l’accès est contrôlé en fonction des règles de sécurité du groupe de sécurité réseau.

> [!IMPORTANT]
> La prise en charge du groupe de sécurité réseau et de l’UDR pour les points de terminaison privés est en préversion publique dans certaines régions. Pour plus d’informations, consultez [Préversion publique de la prise en charge de l’UDR de liaison privée](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) et [Préversion publique de la prise en charge du groupe de sécurité réseau de liaison privée](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/).
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous utilisez le portail pour créer un point de terminaison privé, le paramètre `PrivateEndpointNetworkPolicies` est désactivé automatiquement dans le cadre du processus de création. Un déploiement effectué en utilisant d’autres clients nécessite une étape supplémentaire pour changer ce paramètre. 

Vous pouvez désactiver le paramètre en utilisant :

* Cloud Shell à partir du portail Azure.
* Azure PowerShell
* Azure CLI
* Modèles Microsoft Azure Resource Manager
 
Les exemples suivants montrent comment désactiver `PrivateEndpointNetworkPolicies` pour un réseau virtuel nommé **myVNet**, avec un sous-réseau **par défaut** hébergé dans un groupe de ressources nommé **myResourceGroup**.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="disable-network-policy"></a>Désactiver la stratégie réseau

Cette section explique comment désactiver les stratégies des points de terminaison privés d’un sous-réseau à l’aide d’Azure PowerShell. Utilisez [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) et [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) pour désactiver la stratégie.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```

### <a name="enable-network-policy"></a>Activer la stratégie réseau

Cette section explique comment désactiver les stratégies des points de terminaison privés d’un sous-réseau à l’aide d’Azure PowerShell. Utilisez [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) et [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) pour désactiver la stratégie.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Enabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI

### <a name="disable-network-policy"></a>Désactiver la stratégie réseau

Cette section explique comment désactiver les stratégies des points de terminaison privés d’un sous-réseau à l’aide d’Azure CLI. Utilisez [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) pour désactiver la stratégie.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```

### <a name="enable-network-policy"></a>Activer la stratégie réseau

Cette section explique comment désactiver les stratégies des points de terminaison privés d’un sous-réseau à l’aide d’Azure CLI. Utilisez [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) pour désactiver la stratégie.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies false \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Modèle Resource Manager

### <a name="disable-network-policy"></a>Désactiver la stratégie réseau

Cette section explique comment désactiver les stratégies de point de terminaison privé d’un sous-réseau en utilisant un modèle Azure Resource Manager.

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```

### <a name="enable-network-policy"></a>Activer la stratégie réseau

Cette section explique comment désactiver les stratégies de point de terminaison privé d’un sous-réseau en utilisant un modèle Azure Resource Manager.

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Enabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Étapes suivantes
- Découvrez [Azure Private Endpoint](private-endpoint-overview.md).
 
