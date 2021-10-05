---
title: Attribuer une stratégie d’accès Azure Key Vault (CLI)
description: Comment utiliser Azure CLI pour attribuer une stratégie d’accès Key Vault à un principal de sécurité ou à une identité d’application.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b194bec5f03d5985e282b7c8d220e268de1a763c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838420"
---
# <a name="assign-a-key-vault-access-policy"></a>Attribuer une stratégie d’accès Key Vault

Une stratégie d’accès Key Vault détermine si un principal de sécurité donné, à savoir un utilisateur, une application ou un groupe d’utilisateurs, peut effectuer différentes opérations sur des [secrets](../secrets/index.yml), [clés](../keys/index.yml) et [certificats](../certificates/index.yml) de Key Vault. Vous pouvez attribuer des stratégies d’accès à l’aide du [portail Azure](assign-access-policy-portal.md), d’Azure CLI ou d’[Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

## <a name="assign-an-access-policy"></a>Attribuer une stratégie d’accès

1.  Dans le [portail Azure](https://portal.azure.com), accédez à la ressource Key Vault. 

1.  Sous **Paramètres**, sélectionnez **Stratégies d’accès**, puis sélectionnez **Ajouter une stratégie d’accès** :

    ![Sélectionner des stratégies d’accès, sélection d’Ajouter une attribution de rôle](../media/authentication/assign-policy-portal-01.png)

1.  Sélectionnez les autorisations souhaitées sous **Autorisations de certificat**, **Autorisations de clé** et **Autorisations de secret**. Vous pouvez également sélectionner un modèle contenant des combinaisons d’autorisations courantes :

    ![Spécification des autorisations de stratégie d’accès](../media/authentication/assign-policy-portal-02.png)

1. Sous **Sélectionner le principal**, choisissez le lien **Aucune sélectionnée** pour ouvrir le volet de sélection **Principal**. Entrez le nom de l’utilisateur, de l’application ou du principal de service dans le champ de recherche, sélectionnez le résultat approprié, puis choisissez **Sélectionner**.

    ![Sélection du principal de sécurité pour la stratégie d’accès](../media/authentication/assign-policy-portal-03.png)

    Si vous utilisez une identité managée pour l’application, recherchez et sélectionnez le nom de l’application (Pour plus d’informations sur les principaux de sécurité, consultez [Authentification Key Vault](authentication.md).
 
1.  De retour dans le volet **Ajouter une stratégie d’accès**, sélectionnez **Ajouter** pour enregistrer la stratégie d’accès.

    ![Ajout de la stratégie d’accès avec le principal de sécurité attribué](../media/authentication/assign-policy-portal-04.png)

1. De retour sur la page **Stratégies d’accès**, vérifiez que votre stratégie d’accès figure sous **Stratégies d’accès actuelles**, puis sélectionnez **Enregistrer**. Les stratégies d’accès ne sont pas appliquées tant que vous ne les enregistrez pas.

    ![Enregistrement des changements de stratégie d’accès](../media/authentication/assign-policy-portal-05.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour plus d’informations sur la création de groupes dans Azure Active Directory à l’aide d’Azure CLI, consultez [az ad group create](/cli/azure/ad/group#az_ad_group_create) et [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Configurer Azure CLI et se connecter

1. Pour exécuter des commandes Azure CLI localement, installez [Azure CLI](/cli/azure/install-azure-cli).
 
    Pour exécuter des commandes directement dans le cloud, utilisez le service [Azure Cloud Shell](../../cloud-shell/overview.md).

1. CLI locale uniquement : connectez-vous à Azure à l’aide de la commande `az login` :

    ```bash
    az login
    ```

    La commande `az login` ouvre une fenêtre de navigateur pour recueillir les informations d’identification si nécessaire.

## <a name="acquire-the-object-id"></a>Acquérir l’ID d’objet

Déterminez l’ID d’objet de l’application, du groupe ou de l’utilisateur auquel vous souhaitez attribuer la stratégie d’accès :

- Applications et autres principaux de service : utilisez la commande [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) pour récupérer vos principaux de service. Examinez la sortie de la commande pour déterminer l’ID d’objet du principal de sécurité auquel vous souhaitez attribuer la stratégie d’accès.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Groupes : utilisez la commande [az ad group list](/cli/azure/ad/group#az_ad_group_list), en filtrant les résultats avec le paramètre `--display-name` :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Utilisateurs : utilisez la commande [az ad user show](/cli/azure/ad/user#az_ad_user_show), en passant l’adresse e-mail de l’utilisateur dans le paramètre `--id` :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Attribuer la stratégie d’accès
    
Utilisez la commande [az key vault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) pour attribuer les autorisations souhaitées :

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Remplacez `<object-id>` par l’ID objet de votre principal de sécurité.

Vous avez uniquement besoin d’inclure `--secret-permissions`, `--key-permissions`et `--certificate-permissions` lors de l’attribution d’autorisations à ces types particuliers. Les valeurs autorisées pour `<secret-permissions>`, `<key-permissions>` et `<certificate-permissions>` sont indiquées dans la documentation de la commande [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour plus d’informations sur la création de groupes dans Azure Active Directory à l’aide d’Azure PowerShell, consultez [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) et [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Configurer PowerShell et se connecter

1. Pour exécuter des commandes localement, installez [Azure PowerShell](/powershell/azure/) si ce n’est déjà fait.

    Pour exécuter des commandes directement dans le cloud, utilisez [Azure Cloud Shell](../../cloud-shell/overview.md).

1. PowerShell local uniquement :

    1. Installez le [module Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureAD).

    1. Connectez-vous à Azure :

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Acquérir l’ID d’objet

Déterminez l’ID d’objet de l’application, du groupe ou de l’utilisateur auquel vous souhaitez attribuer la stratégie d’accès :

- Applications et autres principaux de service : utilisez la cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) avec le paramètre `-SearchString` pour filtrer les résultats sur le nom du principal de service souhaité :

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Groupes : utilisez la cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) avec le paramètre `-SearchString` pour filtrer les résultats sur le nom du groupe souhaité :

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    Dans la sortie, l’ID d’objet est indiqué en tant que `Id`.

- Utilisateurs : utilisez la cmdlet [AzADUser](/powershell/module/az.resources/get-azaduser), en transmettant l’adresse e-mail de l’utilisateur au paramètre `-UserPrincipalName`.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    Dans la sortie, l’ID d’objet est indiqué en tant que `Id`.

## <a name="assign-the-access-policy"></a>Attribuer la stratégie d’accès

Utilisez la cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) pour attribuer la stratégie d’accès :

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Vous avez uniquement besoin d’inclure `-PermissionsToSecrets`, `-PermissionsToKeys` et `-PermissionsToCertificates` lors de l’attribution d’autorisations à ces types particuliers. Les valeurs autorisées pour `<secret-permissions>`, `<key-permissions>` et `<certificate-permissions>` sont indiquées dans la documentation [Set-AzKeyVaultAccessPolicy – Paramètres](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters).

---

## <a name="next-steps"></a>Étapes suivantes

- [Sécurité d’Azure Key Vault](security-features.md)
- [Guide du développeur Azure Key Vault](developers-guide.md)
