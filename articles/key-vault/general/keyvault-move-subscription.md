---
title: Déplacement d’un coffre Azure Key Vault vers un autre abonnement | Microsoft Docs
description: Conseils pour le déplacement d’un coffre de clés vers un autre abonnement.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 450aa58d4ad9cbb721e621ec3db8b4ca7e914aa1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121182"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Déplacement d’un coffre Azure Key Vault vers un nouvel abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Vue d’ensemble

**Le déplacement d’un coffre de clés vers un autre abonnement entraîne un changement radical de votre environnement.**

Veillez à bien comprendre l’impact de ce changement et suivez attentivement les conseils prodigués dans cet article avant de décider de déplacer le coffre de clés vers un nouvel abonnement.

Lorsque vous créez un coffre de clés, celui-ci est automatiquement lié à l’ID de client Azure Active Directory par défaut pour l’abonnement dans lequel il est créé. Toutes les entrées de stratégie d’accès sont également liées à cet ID de client. Si vous déplacez votre abonnement Azure d’un locataire A vers un locataire B, vos coffres de clés existants ne sont pas accessibles par les principaux du service (utilisateurs et applications) dans le locataire B. Pour résoudre ce problème, vous devez :

* remplacer l’ID de locataire associé à tous les coffres de clés existants dans l’abonnement par le locataire B ;
* supprimer toutes les entrées de stratégie d’accès existantes ;
* ajouter de nouvelles entrées de stratégie d’accès associées au locataire B.

## <a name="limitations"></a>Limites

Certains principaux du service (utilisateurs et applications) sont liés à un locataire spécifique. Si vous déplacez votre coffre de clés vers un abonnement dans un autre locataire, vous risquez de ne pas pouvoir restaurer l’accès à un principal du service spécifique. Vérifiez que tous les principaux du service essentiels existent dans le locataire vers lequel vous déplacez votre coffre de clés.

## <a name="design-considerations"></a>Remarques relatives à la conception

Votre organisation a peut-être implémenté Azure Policy avec une application ou des exclusions au niveau de l’abonnement. Il peut y avoir un ensemble d’attributions de stratégies dans l’abonnement où se trouve actuellement votre coffre de clés et l’abonnement vers lequel vous déplacez votre coffre de clés. Un conflit dans les exigences de stratégie peut entraîner un dysfonctionnement de vos applications.

### <a name="example"></a>Exemple

Vous avez une application connectée à un coffre de clés qui crée des certificats valides pendant deux ans. L’abonnement vers lequel vous tentez de déplacer votre coffre de clés a une attribution de stratégie qui empêche la création de certificats d’une durée de validité de plus d’un an. Après avoir déplacé votre coffre de clés vers le nouvel abonnement, l’opération de création d’un certificat d’une validité de deux ans est bloquée par une attribution de stratégie Azure.

### <a name="solution"></a>Solution

Accédez à la page Azure Policy dans le Portail Azure et vérifiez que les attributions de stratégie sont les mêmes pour votre abonnement actuel et l’abonnement cible.

## <a name="prerequisites"></a>Prérequis

* Accès de niveau contributeur ou supérieur à l’abonnement actuel dans lequel se trouve votre coffre de clés.
* Accès de niveau contributeur ou supérieur à l’abonnement vers lequel vous souhaitez déplacer votre coffre de clés.
* Groupe de ressources dans le nouvel abonnement.

## <a name="procedure"></a>Procédure

### <a name="initial-steps-moving-key-vault"></a>Étapes initiales (déplacement Key Vault)

1. Connectez-vous au portail Azure.
2. Accédez à votre coffre de clés.
3. Cliquez sur l’onglet « Vue d’ensemble ».
4. Sélectionnez le bouton « Déplacer ».
5. Dans la liste déroulante, sélectionnez l’option « Déplacer vers un autre abonnement ».
6. Sélectionnez le groupe de ressources vers lequel vous souhaitez déplacer votre coffre de clés.
7. Sélectionnez le groupe de ressources vers lequel vous souhaitez déplacer votre coffre de clés.
8. Confirmez la lecture de l’avertissement concernant le déplacement des ressources.
9. Sélectionnez « OK ».

### <a name="additional-steps-post-move"></a>Étapes supplémentaires (après déplacement)

Maintenant que vous avez déplacé votre coffre de clés vers le nouvel abonnement, vous devez mettre à jour l’ID de locataire et supprimer les anciennes stratégies d’accès. Voici des didacticiels pour ces étapes dans PowerShell et Azure CLI.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Maintenant que votre coffre est associé à l’ID de locataire approprié et que les anciennes entrées de stratégie d’accès sont supprimées, définissez les nouvelles entrées de stratégie d’accès avec l’applet de commande Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ou la commande d’interface de ligne de commande Azure [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

Si vous utilisez une identité managée pour les ressources Azure, vous devez également la mettre à jour vers le nouveau locataire Azure AD. Pour plus d’informations sur les identités managées, consultez [Fournir une authentification Key Vault avec une identité managée](managed-identity.md).

Si vous utilisez MSI, vous devrez également mettre à jour l’identité MSI, car l’ancienne identité ne sera plus dans le locataire AAD correct.


