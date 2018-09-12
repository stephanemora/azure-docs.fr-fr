---
ms.assetid: ''
title: Configurer les pare-feux et réseaux virtuels d’Azure Key Vault
description: Instructions détaillées pour configurer les pare-feux et réseaux virtuels d’Azure Key Vault
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346589"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurer les pare-feux et réseaux virtuels d’Azure Key Vault

Ce guide fournit des instructions détaillées pour configurer les pare-feux et réseaux virtuels d’Azure Key Vault afin de restreindre l’accès à votre coffre de clés. Les [points de terminaison de service de réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) permettent de restreindre l’accès au coffre de clés à un réseau virtuel ou à un ensemble de plages d’adresses IPv4 (Internet Protocol version 4) spécifiés.

> [!IMPORTANT]
> Une fois que les règles de pare-feu et de réseau virtuel sont effectives, les opérations de [plan de données](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) d’Azure Key Vault peuvent être effectuées UNIQUEMENT lorsque les demandes de l’appelant proviennent de réseaux virtuels ou de plages d’adresses IPV4 autorisés. Cela s’applique également à l’accès au coffre de clés à partir du portail Azure. Si un utilisateur peut accéder à un coffre de clés à partir du portail Azure, il se peut qu’il ne puisse pas répertorier des clés/secrets/certificats si son ordinateur client ne figure pas dans la liste autorisée. Cela affecte également l’accès au Sélecteur de compte Key Vault par d’autres services Azure. Il se peut que des utilisateurs soient en mesure d’afficher la liste des coffres de clés, mais ils ne peuvent pas afficher les clés si des règles de pare-feu bloquent leur ordinateur client.

## <a name="azure-portal"></a>Portail Azure

1. Accédez au coffre de clés que vous souhaitez sécuriser.
2. Cliquez sur **Pare-feux et réseaux virtuels**.
3. Sous **Autoriser l’accès depuis**, cliquez sur **Réseaux sélectionnés**.
4. Pour ajouter des réseaux virtuels existants à des règles de pare-feux et de réseau virtuel, cliquez sur **+ Ajouter des réseaux virtuels existants**.
5. Dans le nouveau panneau qui s’affiche, sélectionnez l’abonnement, les réseaux virtuels et les sous-réseaux que vous souhaitez autoriser à accéder à ce coffre de clés. Si les réseaux virtuels et sous-réseaux que vous sélectionnez n’ont pas de points de terminaison de service activés, un message s’affiche, indiquant les réseaux qui n’ont pas de points de terminaison de service activé. Cliquez sur **Activer** après avoir confirmé que vous souhaitez activer des points de terminaison de service pour les réseaux virtuels et sous-réseaux répertoriés. Jusqu’à 15 minutes peuvent être nécessaires pour que l’activation soit effective.
6. Vous pouvez également ajouter de nouveaux réseaux virtuels et sous-réseaux, puis activer des points de terminaison de service pour ceux-ci en cliquant sur **+ Ajouter un nouveau réseau virtuel**, puis en suivant les invites.
7. Sous **Réseaux IP**, vous pouvez ajouter des plages d’adresses IPv4 en utilisant [la notation de routage interdomaine sans classe (CIDR)](https://tools.ietf.org/html/rfc4632) ou en entrant des adresses IP individuelles.
8. Cliquez sur **Enregistrer**.

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Installez Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) et [connectez-vous](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Affichez la liste des règles de réseau virtuel disponibles. Si vous n’avez pas défini de règle pour ce coffre de clés, la liste est vide.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Activez un point de terminaison de service pour Azure Key Vault sur un réseau virtuel et un sous-réseau existants.
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Ajoutez une plage d’adresses IP à partir desquelles le trafic est autorisé.
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Si ce coffre de clés doit être accessible à tous les services approuvés, définissez « Ignorer » pour AzureServices.
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. L’étape finale et importante consiste à présent à activer les règles de réseau en définissant l’action par défaut sur « Refuser ».
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Installez la dernière version d’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) et [connectez-vous](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Affichez la liste des règles de réseau virtuel disponibles. Si vous n’avez pas défini de règle pour ce coffre de clés, la liste est vide.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Activez un point de terminaison de service pour Azure Key Vault sur un réseau virtuel et un sous-réseau existants.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Ajoutez une plage d’adresses IP à partir desquelles le trafic est autorisé.
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Si ce coffre de clés doit être accessible à tous les services approuvés, définissez « Ignorer » pour AzureServices.
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. L’étape finale et importante consiste à présent à activer les règles de réseau en définissant l’action par défaut sur « Refuser ».
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Références

* Commandes Azure CLI 2.0 : [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Applets de commande Azure PowerShell : [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Étapes suivantes

* [Points de terminaison de service de réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md)