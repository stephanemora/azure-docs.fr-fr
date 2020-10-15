---
title: Gérer une identité managée attribuée par l’utilisateur – Azure CLI – Azure AD
description: Instructions pas à pas pour créer, lister et supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a1a991ab79c38dad1a89533091d80406615d35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969481"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Créer, répertorier ou supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI


Les identités managées pour ressources Azure fournissent aux services Azure une identité managée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, lister et supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour exécuter les exemples de scripts, vous avez deux options :
    - Utiliser [Azure Cloud Shell](../../cloud-shell/overview.md), que vous pouvez ouvrir à l’aide du bouton **Essayer** dans le coin supérieur droit des blocs de code.
    - Exécuter les scripts localement en installant la dernière version d’[Azure CLI](/cli/azure/install-azure-cli), puis en vous connectant à Azure à l’aide d’[az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure dans lequel vous souhaitez créer des ressources.   

> [!NOTE]
> Pour modifier les autorisations utilisateur en utilisant un principal de service d’application avec l’interface CLI, il est nécessaire de fournir au principal de service des autorisations supplémentaires dans l’API Graph Azure AD, car certaines parties de l’interface CLI effectuent des requêtes GET sur cette API ; à défaut, le message « Privilèges insuffisants pour effectuer l’opération » risque d’apparaître. Pour cela, accédez à l’inscription de l’application dans Azure Active Directory, sélectionnez votre application, cliquez sur Autorisations d’API, faites défiler la liste et sélectionnez Azure Active Directory Graph. Sélectionnez alors Autorisations d’application, puis ajoutez les autorisations nécessaires. 

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur 

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour créer une identité managée affectée par l’utilisateur, utilisez la commande [az identity create](/cli/azure/identity#az-identity-create). Le paramètre `-g` spécifie le groupe de ressources dans lequel créer l’identité managée affectée par l’utilisateur, et le paramètre `-n` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour répertorier les identités affectées par l’utilisateur, utilisez la commande [az identity list](/cli/azure/identity#az-identity-list). Remplacez `<RESOURCE GROUP>` par votre propre valeur :

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

Dans la réponse json, les identités managées affectées par l’utilisateur ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la commande [az identity delete](/cli/azure/identity#az-identity-delete).  Le paramètre -n spécifie son nom, et le paramètre -g spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée. Remplacez les valeurs des paramètres `<USER ASSIGNED IDENTITY NAME>` et `<RESOURCE GROUP>` par vos propres valeurs :

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Supprimez ces références à partir de la machine virtuelle/du groupe de machines virtuelles identiques à l’aide de la commande `az vm/vmss identity remove`.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste complète des commandes d’identité Azure CLI, consultez [az identity](/cli/azure/identity).

Pour plus d’informations sur la façon d’attribuer une identité managée affectée par l’utilisateur à une machine virtuelle Azure, voir [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).
