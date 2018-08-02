---
title: Guide pratique pour gérer une identité MSI (Managed Service Identity) attribuée à l’utilisateur à l’aide d’Azure CLI
description: Instructions étape par étape pour créer, lister et supprimer une identité MSI (Managed Service Identity) attribuée à l’utilisateur à l’aide d’Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5deaace49bfff994defc06a5f60597add6affc0b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188146"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Créer, lister ou supprimer une identité attribuée à l’utilisateur à l’aide d’Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Managed Service Identity fournit aux services Azure une identité administrée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, lister et supprimer une identité attribuée à l’utilisateur à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identité affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions de rôles suivants :
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor), pour créer, lire (lister), mettre à jour et supprimer une identité affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour lire (lister) les propriétés d’une identité affectée par l’utilisateur.
- Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :
    - Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
    - Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
    - [Installez la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser une console CLI locale. Connectez-vous à Azure avec la commande `az login` en utilisant un compte associé à l’abonnement Azure sous lequel vous souhaitez déployer l’identité MSI attribuée à l’utilisateur.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée à l’utilisateur 

Pour créer une identité attribuée à l’utilisateur, utilisez la commande [az identity create](/cli/azure/identity#az-identity-create). Le paramètre `-g` spécifie le groupe de ressources dans lequel créer l’identité attribuée à l’utilisateur, tandis que le paramètre `-n` indique son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lister les identités attribuées à l’utilisateur

Pour lister les identités attribuées à l’utilisateur, utilisez la commande [az identity list](/cli/azure/identity#az-identity-list). Remplacez `<RESOURCE GROUP>` par votre propre valeur :

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Dans la réponse json, les identités utilisateurs ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Supprimer une identité attribuée à l’utilisateur

Pour supprimer une identité attribuée à l’utilisateur, utilisez la commande [az identity delete](/cli/azure/identity#az-identity-delete).  Le paramètre -n spécifie son nom et le paramètre -g spécifie le groupe de ressources où l’identité attribuée à l’utilisateur a été créée. Remplacez les valeurs des paramètres `<USER ASSIGNED IDENTITY NAME>` et `<RESOURCE GROUP>` par vos propres valeurs :

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> La suppression d’une identité attribuée à l’utilisateur ne supprime pas les références des ressources auxquelles elle a été attribuée. Supprimez ces références à partir de la machine virtuelle/du groupe de machines virtuelles identiques à l’aide de la commande `az vm/vmss identity remove`.

## <a name="related-content"></a>Contenu connexe

Pour obtenir une liste complète des commandes d’identité Azure CLI, consultez [az identity](/cli/azure/identity).

Pour plus d’informations sur l’attribution d’une identité attribuée à l’utilisateur sur une machine virtuelle Azure, consultez [Configurer Managed Service Identity (MSI) à l’aide d’Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-identity).


 
