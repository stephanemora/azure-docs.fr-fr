---
title: Changer l’ID de locataire du coffre de clés après un déplacement d’abonnement - Azure Key Vault | Microsoft Docs
description: Apprenez à changer l’ID de client d’un coffre de clés après le déplacement d’un abonnement vers un autre client
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 2159b5b515e22458edf3ba0eb5b6f23f3f37ce95
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990106"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modifier l’ID de client du coffre de clés après un déplacement d’abonnement

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q : Mon abonnement a été déplacé du client A vers le client B. Comment modifier l’ID de client du coffre de clés existant et définir des ACL correctes pour les principaux dans le client B ?

Lorsque vous créez un coffre de clés dans un abonnement, il est automatiquement lié à l’ID de client Azure Active Directory par défaut pour cet abonnement. Toutes les entrées de stratégie d’accès sont également liées à cet ID de client. Lorsque vous déplacez votre abonnement Azure du client A vers le client B, vos coffres de clés existants ne sont pas accessibles par les principaux (utilisateurs et applications) dans le client B. Pour résoudre ce problème, vous devez :

* modifier l’ID de client associé à tous les coffres de clés existants dans cet abonnement pour le client B ;
* supprimer toutes les entrées de stratégie d’accès existantes ;
* ajouter de nouvelles entrées de stratégie d’accès associées au client B.

Par exemple, si vous avez un coffre de clés 'myvault' dans un abonnement qui a été déplacé du client A vers le client B, voici comment modifier l’ID de client de ce coffre de clés et supprimer d’anciennes stratégies d’accès.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

Dans la mesure où ce coffre se trouvait dans le client A avant son déplacement, la valeur d’origine de **$vault. Properties.TenantId** est le client A, tandis que **(Get-AzContext).Tenant.TenantId** est le client B.

Maintenant que votre coffre est associé à l’ID de client qui convient et que les anciennes entrées de stratégie d’accès sont supprimées, définissez les nouvelles entrées de stratégie d’accès avec [Set-AzVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

## <a name="next-steps"></a>Étapes suivantes

Pour toute question concernant le coffre de clés Azure, rendez-vous sur les [forums sur les coffres de clés Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
