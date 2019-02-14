---
title: Compte de stockage managé Azure Key Vault - version PowerShell
description: La fonctionnalité de compte de stockage managé assure une intégration transparente entre Azure Key Vault et un compte de stockage Azure.
ms.topic: conceptual
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: barbkess
ms.date: 11/28/2018
ms.openlocfilehash: 3566f7514f10bc8fb1de417583c6db17bb4e091e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104973"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Compte de stockage managé Azure Key Vault - PowerShell

> [!NOTE]
> [L’intégration du stockage Azure avec Azure Active Directory (Azure AD) est désormais disponible en préversion](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Nous recommandons d’utiliser Azure AD pour l’authentification et l’autorisation, offrant un accès au stockage Azure via un jeton OAuth2, à l’instar d’Azure Key Vault. Vous pouvez ainsi :
> - Authentifier votre application cliente en utilisant une identité d’application ou d’utilisateur plutôt que les informations d’identification du compte de stockage. 
> - Utiliser une [identité Azure AD managée](/azure/active-directory/managed-identities-azure-resources/) lors de l’exécution sur Azure. Les identités managées suppriment totalement l’authentification du client ainsi que le stockage des informations d’identification dans ou avec votre application.
> - Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations, ce qui est également pris en charge par Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Un [compte de stockage Azure](/azure/storage/storage-create-storage-account) utilise des informations d’identification qui se composent d’un nom de compte et d’une clé. La clé est générée automatiquement et sert plus de « mot de passe » que de clé de chiffrement. Key Vault peut gérer ces clés de compte de stockage en les stockant en tant que [secrets Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Vue d’ensemble

La fonctionnalité de compte de stockage Key Vault managé exécute plusieurs fonctions de gestion interne à votre place :

- Répertorie (synchronise) les clés avec un compte de stockage Azure.
- Regénère (fait tourner) les clés régulièrement.
- Gère les clés des comptes de stockage et des comptes de stockage classiques.
- Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.

Lorsque vous utilisez la fonctionnalité de clé de compte de stockage managé :

- **Autorisez uniquement Key Vault à gérer vos clés de compte de stockage.** N’essayez pas de les gérer vous-même, vous interféreriez avec les processus de Key Vault.
- **Ne permettez pas que les clés de compte de stockage soient gérées par plusieurs objets Key Vault**.
- **Ne régénérez pas manuellement vos clés de compte de stockage**. Nous vous recommandons de les régénérer via Key Vault.

L’exemple suivant vous montre comment autoriser Key Vault à gérer vos clés de compte de stockage.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autoriser Key Vault à accéder à votre compte de stockage

> [!IMPORTANT]
> Un locataire Azure AD fournit à chaque application inscrite un **[principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object)**, qui représente l’identité de l’application. L’ID d’application du principal de service est utilisé quand il reçoit l’autorisation d’accéder à d’autres ressources Azure, via le contrôle d’accès en fonction du rôle (RBAC). Key Vault étant une application Microsoft, elle est préinscrite dans tous les locataires Azure AD sous le même ID d’application dans chaque cloud Azure :
> - Les locataires Azure AD dans le cloud Azure Government utilisent l’ID d’application `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Les locataires Azure AD dans le cloud public Azure et tous les autres utilisent l’ID d’application `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Pour permettre à Key Vault d’accéder et de gérer vos clés de compte de stockage, vous devez l’autoriser à accéder à votre compte de stockage. L’application Key Vault a besoin d’autorisations afin de *répertorier* et de *regénérer* des clés pour votre compte de stockage. Ces autorisations sont activées par le biais du rôle RBAC intégré [Rôle de service d’opérateur de clé de compte de stockage](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Attribuez ce rôle au principal de service Key Vault, en limitant la portée à votre compte de stockage, à l’aide des étapes ci-dessous. Veillez à mettre à jour les variables `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey` et `$keyVaultName` avant d’exécuter le script :

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Si l’attribution du rôle a réussi, le résultat devrait ressembler à l’exemple suivant :

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Si Key Vault a déjà été ajouté au rôle sur votre compte de stockage, vous recevrez un message d’erreur *« L'attribution de rôle existe déjà »*. erreur. Vous pouvez également vérifier l’attribution du rôle à la page « Contrôle d’accès (IAM) » du compte de stockage sur le portail Azure.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Autoriser votre compte d’utilisateur à accéder à des comptes de stockage managés

>[!TIP] 
> Tout comme Azure AD fournit un **principal de service** pour l’identité d’une application, un **utilisateur principal** est fourni pour l’identité d’un utilisateur. L’utilisateur principal peut ainsi être autorisé à accéder à Key Vault via les stratégie d’accès Key Vault.

Dans la même session PowerShell, mettez à jour la stratégie d’accès Key Vault pour les comptes de stockage managés. Cette étape applique des autorisations de compte de stockage à votre compte d’utilisateur pour garantir votre accès aux fonctionnalités du compte de stockage managé : 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Notez que les autorisations pour les comptes de stockage ne sont pas disponibles sur la page « Stratégies d’accès » du compte de stockage dans le portail Azure.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Ajouter un compte de stockage managé à votre instance Key Vault

Dans la même session PowerShell, créez un compte de stockage managé dans votre instance Key Vault. Le commutateur `-DisableAutoRegenerateKey` spécifie de ne PAS régénérer les clés de compte de stockage.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Si le compte de stockage a été correctement créé sans régénération des clés, le résultat devrait ressembler à l’exemple suivant :

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Activer la régénération des clés

Si vous souhaitez que Key Vault régénère régulièrement les clés de votre compte de stockage, vous pouvez définir une période de régénération. Dans l’exemple suivant, nous définissons une période de régénération de trois jours. Après trois jours, Key Vault regénère « key1 » et remplace la clé active « key2 » par « key1 ».

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Si le compte de stockage a été correctement créé avec régénération des clés, le résultat devrait ressembler à l’exemple suivant :

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de clés de compte de stockage managé](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Présentation des clés, des secrets et des certificats](about-keys-secrets-and-certificates.md)
- [Informations de référence PowerShell sur Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
