---
title: Accorder aux applications l’autorisation d’accéder à un coffre de clés Azure - Azure Key Vault | Microsoft Docs
description: Découvrez comment accorder à plusieurs applications l'autorisation d'accéder à un coffre de clés
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 21cb7133bad27013895a5e717cb7729b71795ce9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078960"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Fournir une authentification Key Vault avec une stratégie de contrôle d’accès

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Le moyen le plus simple d’authentifier une application cloud auprès de Key Vault consiste à utiliser une identité managée. Pour plus d’informations, consultez [Utiliser une identité managée App Service pour accéder à Azure Key Vault](managed-identity.md).  Si vous créez une application locale, si vous effectuez du développement local ou si vous ne parvenez pas à utiliser une identité managée, vous pouvez à la place inscrire un principal de service manuellement et fournir l’accès au coffre de clés à l’aide d’une stratégie de contrôle d’accès.  

Le coffre de clés prend en charge jusqu’à 1 024 entrées de stratégie d’accès, chaque entrée accordant un ensemble distinct d’autorisations à un « principal » :   Par exemple, voici comment l’application console décrite dans le [guide de démarrage rapide de la bibliothèque de client Azure Key Vault pour .NET](../secrets/quick-create-net.md) accède au coffre de clés.

Pour plus d’informations sur le contrôle d’accès Key Vault, consultez [Sécurité d’Azure Key Vault : Gestion de l’identité et de l’accès](overview-security.md#identity-and-access-management). Pour obtenir des informations complètes sur le contrôle d’accès, consultez : 

- [Clés](../keys/index.yml)
- [Contrôle d’accès aux secrets](../secrets/index.yml)
- [Contrôle d’accès aux certificats](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

- Un coffre de clés. Vous pouvez utiliser un coffre de clés existant ou en créer un nouveau en suivant les étapes d’un des guides de démarrage rapide suivants :
   - [Créer un coffre de clés avec Azure CLI](../secrets/quick-create-cli.md)
   - [Créer un coffre de clés avec Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Créer un coffre de clés avec le portail Azure](../secrets/quick-create-portal.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/). Vous pouvez également utiliser le [portail Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Accorder l’accès à votre coffre de clés

Chaque entrée de stratégie d’accès au coffre de clés accorde un ensemble distinct d’autorisations à un principal :

- **Une application** : s’il s’agit d’une application cloud, vous devez plutôt [utiliser une identité managée pour accéder à Azure Key Vault](managed-identity.md), si possible.
- **Un groupe Azure AD** : bien que le coffre de clés prenne en charge uniquement 1 024 entrées de stratégie d’accès, vous pouvez ajouter plusieurs applications et utilisateurs à un même groupe Azure AD, puis ajouter ce groupe en tant qu’entrée unique à votre stratégie de contrôle d’accès.
- **Un utilisateur** : donner aux utilisateurs un accès direct à un coffre de clés est **déconseillé**. Dans l’idéal, les utilisateurs doivent être ajoutés à un groupe Azure AD, qui lui-même a accès au coffre de clés. Consultez [Sécurité d’Azure Key Vault : Gestion de l’identité et de l’accès](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Obtenir l’objectID

Pour permettre à une application, à un groupe Azure AD ou à un utilisateur d’accéder à votre coffre de clés, vous devez d’abord obtenir son objectId.

#### <a name="applications"></a>Applications

L’objectId d’une application correspond à son principal de service associé. Pour plus d’informations sur les principaux de service, consultez [Objets application et principal du service dans Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Il existe deux façons d’obtenir un objectId pour une application.  La première consiste à inscrire votre application auprès d’Azure Active Directory. Pour ce faire, suivez les étapes du guide de démarrage rapide [Inscrire une application à l’aide de la plateforme d’identités Microsoft](../../active-directory/develop/quickstart-register-app.md). Une fois l’inscription effectuée, l’objectID est listé en tant qu’« ID d’application (client) ».

La seconde consiste à créer un principal de service dans une fenêtre de terminal. Avec Azure CLI, utilisez la commande [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) et fournissez un nom de principal du service unique à l’indicateur -n au format « http://&lt;mon-nom-de-principal-du-service-unique&gt; ».

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name"
```

L’objectId est listé dans la sortie en tant que `clientID`.

Avec Azure PowerShell, utilisez l’applet de commande [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0).


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principal-name>
```

L’objectId est listé dans la sortie en tant que `Id` (et non `ApplicationId`).

#### <a name="azure-ad-groups"></a>Groupes Azure AD

Vous pouvez ajouter plusieurs applications et utilisateurs à un groupe Azure AD, puis accorder à ce groupe l’accès à votre coffre de clés.  Pour plus d’informations, consultez la section [Création et ajout de membres à un groupe Azure AD](#creating-and-adding-members-to-an-azure-ad-group) ci-dessous.

Pour rechercher l’objectId d’un groupe Azure AD avec Azure CLI, utilisez la commande [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list). En raison du grand nombre de groupes qui peuvent exister dans votre organisation, vous devez également fournir une chaîne de recherche au paramètre `--display-name`.

```azurecli-interactive
az ad group list --display-name <search-string>
```
L’objectId est retourné dans le JSON :

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Pour rechercher l’objectId d’un groupe Azure AD avec Azure PowerShell, utilisez l’applet de commande [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0). En raison du grand nombre de groupes qui peuvent exister dans votre organisation, vous serez probablement amené à fournir également une chaîne de recherche au paramètre `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Dans la sortie, l’objectId est listé en tant que `Id` :

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Utilisateurs

Vous pouvez également ajouter un utilisateur individuel à la stratégie de contrôle d’accès d’un coffre de clés. **Nous vous le déconseillons.** Nous vous encourageons plutôt à ajouter des utilisateurs à un groupe Azure AD et à ajouter le groupe aux stratégies.

Si vous souhaitez néanmoins rechercher un utilisateur à l’aide d’Azure CLI, utilisez la commande [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) en passant l’adresse e-mail des utilisateurs au paramètre `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

L’objectId de l’utilisateur est retourné dans la sortie :

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Pour rechercher un utilisateur avec Azure PowerShell, utilisez l’applet de commande [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) en passant l’adresse e-mail des utilisateurs au paramètre `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

L’objectId de l’utilisateur est retourné dans la sortie en tant que `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Accorder au principal l’accès à votre coffre de clés

Une fois que vous avez l’objectID de votre principal, vous pouvez créer une stratégie d’accès pour votre coffre de clés. Cela lui permet de disposer des autorisations nécessaires pour obtenir, lister, définir et supprimer les clés et les secrets ainsi que toutes les autorisations supplémentaires souhaitées.

Avec Azure CLI, cette opération s’effectue en passant l’objectId à la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Avec Azure PowerShell, cette opération s’effectue en passant l’objectId à l’applet de commande [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Création et ajout de membres à un groupe Azure AD

Vous pouvez créer un groupe Azure AD, lui ajouter des applications et des utilisateurs, et lui donner accès à votre coffre de clés.  Cela vous permet d’ajouter un certain nombre d’applications à un coffre de clés sous la forme d’une seule entrée de stratégie d’accès. Ainsi, il n’est plus nécessaire de donner aux utilisateurs un accès direct à votre coffre de clés (ce que nous déconseillons). Pour plus d’informations, consultez [Gérer l’accès aux applications et aux ressources à l’aide de groupes Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Autres composants requis

En plus des [prérequis ci-dessus](#prerequisites), vous devez disposer des autorisations nécessaires pour créer/modifier des groupes dans votre locataire Azure Active Directory. Si vous ne disposez pas des autorisations, vous devrez peut-être contacter votre administrateur Active Directory Azure.

Si vous comptez utiliser PowerShell, vous allez également avoir besoin du [module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Créer un groupe Azure Active Directory

Créez un groupe Azure Active Directory à l’aide de la commande Azure CLI [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) ou de l’applet de commande Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0).


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Dans les deux cas, notez le GroupId des groupes qui viennent d’être créés, car vous allez en avoir besoin pour les étapes ci-dessous.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Rechercher les objectId de vos applications et utilisateurs

Vous pouvez rechercher les objectId de vos applications à l’aide d’Azure CLI en utilisant la commande [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) et le paramètre `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

Recherchez les objectId de vos applications à l’aide d’Azure PowerShell en utilisant l’applet de commande [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) et en passant une chaîne de recherche au paramètre `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Pour rechercher les objectId de vos utilisateurs, suivez les étapes décrites dans la section [Utilisateurs](#users) ci-dessus.

### <a name="add-your-applications-and-users-to-the-group"></a>Ajouter vos applications et vos utilisateurs au groupe

Ajoutez à présent les objectId au groupe Azure AD que vous venez de créer.

Avec Azure CLI, utilisez [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add) en passant l’objectId au paramètre `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Avec Azure PowerShell, utilisez l’applet de commande [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) en passant l’objectId au paramètre `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Accorder au groupe AD l’accès à votre coffre de clés

Enfin, accordez les autorisations de groupe AD à votre coffre de clés à l’aide de la commande Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) ou de l’applet de commande Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). Pour obtenir des exemples, consultez la section qui explique comment [permettre à une application, à un groupe Azure AD ou à un utilisateur d’accéder à votre coffre de clés](#give-the-principal-access-to-your-key-vault), ci-dessus.

L’application nécessite également l’affectation d’au moins un rôle IAM (gestion des identités et des accès) au coffre de clés. Sinon, elle ne pourra pas se connecter en raison de droits insuffisants pour accéder à l’abonnement.

> [!WARNING]
> Les groupes Azure AD avec des identités managées peuvent nécessiter jusqu’à 8 heures pour actualiser le jeton et devenir effectifs.

## <a name="next-steps"></a>Étapes suivantes

- [Sécurité d’Azure Key Vault : Gestion des identités et des accès](overview-security.md#identity-and-access-management)
- [Fournir une authentification Key Vault avec une identité managée App Service](managed-identity.md)
- [Sécuriser votre coffre de clés](secure-your-key-vault.md)
- [Guide du développeur Azure Key Vault](developers-guide.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](best-practices.md)
