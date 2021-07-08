---
title: Configuration de la mise en réseau Azure Key Vault
description: Instructions détaillées pour configurer les pare-feux et réseaux virtuels d’Azure Key Vault
services: key-vault
author: sebansal
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 5/11/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: d6035436a005012cf67a46302213f79b2dce1f59
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109850187"
---
# <a name="configure-azure-key-vault-networking-settings"></a>Configurer les paramètres de mise en réseau Azure Key Vault

Cet article vous fournit des conseils sur la façon de configurer les paramètres de mise en réseau Azure Key Vault pour qu’ils fonctionnent avec d’autres applications et services Azure. Pour en savoir plus sur les différentes configurations de sécurité réseau, [consultez cette page](network-security.md).

Voici les instructions pas à pas pour configurer le pare-feu et les réseaux virtuels Key Vault à l’aide du portail Azure, d’Azure CLI et d’Azure PowerShell

# <a name="portal"></a>[Portail](#tab/azure-portal)


1. Accédez au coffre de clés que vous voulez sécuriser.
2. Sélectionnez **Mise en réseau**, puis l’onglet **Pare-feu et réseaux virtuels**.
3. Sous **Autoriser l’accès depuis**, cliquez sur **Réseaux sélectionnés**.
4. Pour ajouter des réseaux virtuels existants à des règles de pare-feux et de réseau virtuel, sélectionnez **+ Ajouter des réseaux virtuels existants**.
5. Dans le nouveau panneau qui s’ouvre, sélectionnez l’abonnement, les réseaux virtuels et les sous-réseaux que vous voulez autoriser à accéder à ce coffre de clés. Si les réseaux virtuels et les sous-réseaux que vous sélectionnez n’ont pas de points de terminaison de service est activés, confirmez que vous voulez activer les points de terminaison de service, puis sélectionnez **Activer**. Jusqu’à 15 minutes peuvent être nécessaires pour que l’activation soit effective.
6. Sous **Réseaux IP**, ajoutez des plages d’adresses IPv4 en utilisant [la notation CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) ou en entrant des adresses IP individuelles.
7. Si vous souhaitez autoriser les services Microsoft approuvés à contourner le pare-feu Key Vault, sélectionnez « Oui ». Pour obtenir la liste complète des services approuvés par Key Vault actuels, consultez le lien suivant. [Services approuvés par Azure Key Vault](./overview-vnet-service-endpoints.md#trusted-services)
7. Sélectionnez **Enregistrer**.

Vous pouvez également ajouter de nouveaux réseaux virtuels et des sous-réseaux, puis activer des points de terminaison de service pour ceux-ci en sélectionnant **+ Ajouter un nouveau réseau virtuel**. Puis suivez les invites.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Voici comment configurer les pare-feux et les réseaux virtuels de Key Vault avec Azure CLI

1. [Installez Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voici comment configurer les pare-feux et les réseaux virtuels Key Vault avec PowerShell :

1. Installez la dernière version d’[Azure PowerShell](/powershell/azure/install-az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

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
---
## <a name="references"></a>References
* Informations de référence sur les modèles ARM : [Informations de référence sur les modèles ARM pour Azure Key Vault](/azure/templates/Microsoft.KeyVault/vaults)
* Commandes Azure CLI : [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Applets de commande Azure PowerShell : [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Étapes suivantes

* [Points de terminaison de service de réseau virtuel pour Azure Key Vault](overview-vnet-service-endpoints.md)
* [Présentation de la sécurité Azure Key Vault](security-features.md)
