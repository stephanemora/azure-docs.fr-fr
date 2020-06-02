---
title: Changer l’ID de locataire du coffre de clés après un déplacement d’abonnement - Azure Key Vault | Microsoft Docs
description: Apprenez à changer l’ID de client d’un coffre de clés après le déplacement d’un abonnement vers un autre client
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 9e35f5c9288860056a910f54f9601b2178a628bb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828081"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modifier l’ID de client du coffre de clés après un déplacement d’abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Lorsque vous créez un coffre de clés dans un abonnement, il est automatiquement lié à l’ID de client Azure Active Directory par défaut pour cet abonnement. Toutes les entrées de stratégie d’accès sont également liées à cet ID de client. 

Si vous déplacez votre abonnement Azure du locataire A vers le locataire B, vos coffres de clés existants ne sont pas accessibles par les principaux (utilisateurs et applications) dans le locataire B. Pour résoudre ce problème, vous devez :

* remplacer l’ID de locataire associé à tous les coffres de clés existants dans l’abonnement par le locataire B ;
* supprimer toutes les entrées de stratégie d’accès existantes ;
* ajouter de nouvelles entrées de stratégie d’accès associées au locataire B.

Par exemple, si vous avez un coffre de clés « myvault » dans un abonnement qui a été déplacé du locataire A vers le locataire B, vous pouvez utiliser Azure PowerShell pour modifier l’ID de locataire et supprimer d’anciennes stratégies d’accès.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Vous pouvez aussi utiliser l’interface de ligne de commande Azure.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Maintenant que votre coffre est associé à l’ID de locataire approprié et que les anciennes entrées de stratégie d’accès sont supprimées, définissez les nouvelles entrées de stratégie d’accès avec l’applet de commande Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ou la commande d’interface de ligne de commande Azure [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

Si vous utilisez une identité managée pour les ressources Azure, vous devez également la mettre à jour vers le nouveau locataire Azure AD. Pour plus d’informations sur les identités managées, consultez [Fournir une authentification Key Vault avec une identité managée](managed-identity.md).


Si vous utilisez MSI, vous devrez également mettre à jour l’identité MSI, car l’ancienne identité ne sera plus dans le locataire AAD correct.

## <a name="next-steps"></a>Étapes suivantes

Pour toute question concernant Azure Key Vault, rendez-vous sur la [Page de questions Microsoft Q&A consacrée à Azure Key Vault](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
