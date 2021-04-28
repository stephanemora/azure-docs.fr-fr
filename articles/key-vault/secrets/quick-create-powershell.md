---
title: 'Démarrage rapide : Définir et récupérer un secret depuis Key Vault à l’aide de PowerShell'
description: Dans ce guide de démarrage rapide, découvrez comment créer, récupérer et supprimer des secrets dans un coffre de clés Azure Key Vault avec Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 8a0ebfc90fb57a6e67d7c9e41b78d9db502b2720
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814637"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide de PowerShell

Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md). Dans ce démarrage rapide, vous allez utiliser PowerShell pour créer un coffre de clés. Ensuite, vous stockerez un secret dans le coffre que vous venez de créer.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell localement, ce tutoriel nécessite le module Azure PowerShell version 5.0.0 ou ultérieure. Tapez `$PSVersionTable.PSVersion` pour connaître la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Donner à votre compte d’utilisateur des autorisations pour gérer les secrets dans Key Vault

Utilisez l’applet de commande Azure PowerShell Set-AzKeyVaultAccessPolicy pour mettre à jour la stratégie d’accès au coffre de clés et accorder des autorisations de secret à votre compte d’utilisateur.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Ajout d’un secret au coffre de clés

Pour ajouter un secret au coffre, vous devez effectuer deux opérations. Dans ce cas précis, vous ajoutez un mot de passe qu’une application est susceptible d’utiliser. Le mot de passe est appelé **ExamplePassword** et stocke la valeur **hVFkk965BuUv**.

Commencez par convertir la valeur **hVFkk965BuUv** en une chaîne sécurisée en tapant la commande suivante :

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

Ensuite, utilisez la commande Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) pour créer dans Key Vault un secret appelé **ExamplePassword**, avec la valeur **hVFkk965BuUv** :


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Récupérer un secret à partir de Key Vault

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Vous venez de créer un coffre de clés, d’y stocker un secret et de récupérer ce dernier.

## <a name="clean-up-resources"></a>Nettoyer les ressources

 D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, le coffre Key Vault et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés Key Vault et vous y avez stocké un secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Découvrir la procédure de [stockage des secrets multilignes dans Key Vault](multiline-secrets.md)
- Consulter la référence des [applets de commande Azure PowerShell Key Vault](/powershell/module/az.keyvault/#key_vault)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-features.md)
