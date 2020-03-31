---
title: Gérer une identité managée attribuée par l’utilisateur – Azure CLI – Azure AD
description: Instructions détaillées pour créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231809"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Créer, répertorier ou supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI


Les identités managées pour ressources Azure fournissent aux services Azure une identité managée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI.

## <a name="prerequisites"></a>Conditions préalables requises

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :
    - Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
    - Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
    - [Installez la dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou ultérieure) si vous préférez utiliser une console CLI locale. Connectez-vous à Azure avec la commande `az login` en utilisant un compte associé à l’abonnement Azure sous lequel vous souhaitez déployer l’identité managée affectée par l’utilisateur.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur 

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Pour créer une identité managée affectée par l’utilisateur, utilisez la commande [az identity create](/cli/azure/identity#az-identity-create). Le paramètre `-g` spécifie le groupe de ressources dans lequel créer l’identité managée affectée par l’utilisateur, et le paramètre `-n` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Pour répertorier les identités affectées par l’utilisateur, utilisez la commande [az identity list](/cli/azure/identity#az-identity-list). Remplacez `<RESOURCE GROUP>` par votre propre valeur :

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Dans la réponse json, les identités managées affectées par l’utilisateur ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la commande [az identity delete](/cli/azure/identity#az-identity-delete).  Le paramètre -n spécifie son nom, et le paramètre -g spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée. Remplacez les valeurs des paramètres `<USER ASSIGNED IDENTITY NAME>` et `<RESOURCE GROUP>` par vos propres valeurs :

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Supprimez ces références à partir de la machine virtuelle/du groupe de machines virtuelles identiques à l’aide de la commande `az vm/vmss identity remove`.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste complète des commandes d’identité Azure CLI, consultez [az identity](/cli/azure/identity).

Pour plus d’informations sur la façon d’attribuer une identité managée affectée par l’utilisateur à une machine virtuelle Azure, voir [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).


 
