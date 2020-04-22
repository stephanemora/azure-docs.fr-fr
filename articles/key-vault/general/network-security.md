---
title: Configurer les pare-feux et réseaux virtuels d’Azure Key Vault – Azure Key Vault
description: Instructions détaillées pour configurer les pare-feux et réseaux virtuels d’Azure Key Vault
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 93fb718c28d015162d3c43d0936bbf4de9cad2fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425348"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurer les pare-feux et réseaux virtuels d’Azure Key Vault

Cet article fournit des instructions détaillées pour configurer les pare-feux et réseaux virtuels d’Azure Key Vault afin de restreindre l’accès à votre coffre de clés. Les [points de terminaison de service de réseau virtuel pour Key Vault](overview-vnet-service-endpoints.md) permettent de restreindre l’accès à un réseau virtuel et à un ensemble de plages d’adresses IPv4 (Internet Protocol version 4) spécifiés.

> [!IMPORTANT]
> Une fois que les règles de pare-feu sont effectives, les utilisateurs peuvent effectuer des opérations du [plan de données](secure-your-key-vault.md#data-plane-access-control) de Key Vault seulement quand leurs demandes proviennent de réseaux virtuels ou de plages d’adresses IPv4 autorisés. Ceci s’applique également à l’accès au coffre de clés à partir du portail Azure. Si des utilisateurs peuvent accéder à un coffre de clés à partir du portail Azure, il ne peuvent pas lister les clés/secrets/certificats si leur ordinateur client ne figure pas dans la liste autorisée. Ceci affecte également l’accès au sélecteur de compte Key Vault par d’autres services Azure. Des utilisateurs qui peuvent voir la liste des coffres de clés ne peuvent pas lister les clés si des règles de pare-feu bloquent leur ordinateur client.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Voici comment configurer les pare-feux et les réseaux virtuels Key Vault avec le portail Azure :

1. Accédez au coffre de clés que vous voulez sécuriser.
2. Sélectionnez **Mise en réseau**, puis l’onglet **Pare-feu et réseaux virtuels**.
3. Sous **Autoriser l’accès depuis**, cliquez sur **Réseaux sélectionnés**.
4. Pour ajouter des réseaux virtuels existants à des règles de pare-feux et de réseau virtuel, sélectionnez **+ Ajouter des réseaux virtuels existants**.
5. Dans le nouveau panneau qui s’ouvre, sélectionnez l’abonnement, les réseaux virtuels et les sous-réseaux que vous voulez autoriser à accéder à ce coffre de clés. Si les réseaux virtuels et les sous-réseaux que vous sélectionnez n’ont pas de points de terminaison de service est activés, confirmez que vous voulez activer les points de terminaison de service, puis sélectionnez **Activer**. Jusqu’à 15 minutes peuvent être nécessaires pour que l’activation soit effective.
6. Sous **Réseaux IP**, ajoutez des plages d’adresses IPv4 en utilisant [la notation CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) ou en entrant des adresses IP individuelles.
7. Sélectionnez **Enregistrer**.

Vous pouvez également ajouter de nouveaux réseaux virtuels et des sous-réseaux, puis activer des points de terminaison de service pour ceux-ci en sélectionnant **+ Ajouter un nouveau réseau virtuel**. Puis suivez les invites.

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure 

Voici comment configurer les pare-feux et les réseaux virtuels de Key Vault avec Azure CLI

1. [Installez Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et [connectez-vous](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Listez les règles de réseau virtuel disponibles. Si vous n’avez pas défini de règles pour ce coffre de clés, la liste est vide.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Activez un point de terminaison de service pour Key Vault sur un réseau virtuel et un sous-réseau existants.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Ajoutez une plage d’adresses IP à partir de laquelle autoriser le trafic.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Si ce coffre de clés doit être accessible à tous les services approuvés, définissez `bypass` sur `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Activez les règles de réseau en définissant l’action par défaut sur `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voici comment configurer les pare-feux et les réseaux virtuels Key Vault avec PowerShell :

1. Installez la dernière version d’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) et [connectez-vous](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Listez les règles de réseau virtuel disponibles. Si vous n’avez pas défini de règles pour ce coffre de clés, la liste est vide.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Activez un point de terminaison de service pour Azure Key Vault sur un réseau virtuel et un sous-réseau existants.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Ajoutez une plage d’adresses IP à partir de laquelle autoriser le trafic.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Si ce coffre de clés doit être accessible à tous les services approuvés, définissez `bypass` sur `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Activez les règles de réseau en définissant l’action par défaut sur `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>References

* Commandes Azure CLI : [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Applets de commande Azure PowerShell : [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Étapes suivantes

* [Points de terminaison de service de réseau virtuel pour Key Vault](overview-vnet-service-endpoints.md)
* [Sécuriser votre coffre de clés](secure-your-key-vault.md)
