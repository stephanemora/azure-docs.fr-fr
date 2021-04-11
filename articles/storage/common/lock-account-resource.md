---
title: Appliquer un verrou Azure Resource Manager à un compte de stockage
titleSuffix: Azure Storage
description: Découvrez comment appliquer un verrou Azure Resource Manager à un compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799783"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Appliquer un verrou Azure Resource Manager à un compte de stockage

Microsoft recommande de verrouiller tous vos comptes de stockage avec un verrou Azure Resource Manager afin d’éviter toute suppression accidentelle ou malveillante du compte de stockage. Il existe deux types de verrous de ressources Azure Resource Manager :

- Un verrou **CannotDelete** empêche les utilisateurs de supprimer un compte de stockage, mais autorise la lecture et la modification de sa configuration.
- Un verrou **ReadOnly** empêche les utilisateurs de supprimer un compte de stockage ou de modifier sa configuration, mais autorise la lecture de la configuration.

Pour plus d’informations sur les verrous Azure Resource Manager, consultez [Verrouiller les ressources pour empêcher les modifications](../../azure-resource-manager/management/lock-resources.md).

> [!CAUTION]
> Le verrouillage d’un compte de stockage ne protège pas contre la suppression ou le remplacement des conteneurs ou blobs qui en font partie. Pour plus d’informations sur la protection des données de blob, consultez [Vue d’ensemble de la protection des données](../blobs/data-protection-overview.md).

## <a name="configure-an-azure-resource-manager-lock"></a>Configurer un verrou Azure Resource Manager

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour configurer un verrou sur un compte de stockage avec le portail Azure, effectuez les étapes suivantes :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Dans la section **Paramètres**, sélectionnez **Verrous**.
1. Sélectionnez **Ajouter**.
1. Fournissez un nom pour le verrou de la ressource, et spécifiez le type de verrou. Ajoutez un commentaire sur le verrou, le cas échant.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Capture d’écran montrant comment verrouiller un compte de stockage avec un verrou CannotDelete":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour configurer un verrou sur un compte de stockage avec PowerShell, vous devez d’abord vous assurer d’avoir installé le [module Az PowerShell](https://www.powershellgallery.com/packages/Az). Ensuite, appelez la commande [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) et spécifiez le type de verrou que vous souhaitez créer, comme indiqué dans l’exemple suivant :

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer un verrou sur un compte de stockage avec Azure CLI, appelez la commande [az lock create](/cli/azure/lock#az_lock_create) et spécifiez le type de verrou que vous souhaitez créer, comme illustré dans l’exemple suivant :

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Autoriser les opérations sur les données lorsqu’un verrou ReadOnly est activé

Lorsqu’un verrou **ReadOnly** est appliqué à un compte de stockage, l’opération [List Keys](/rest/api/storagerp/storageaccounts/listkeys) est bloquée pour ce compte de stockage. L’opération **List Keys** est une opération POST HTTPS, et toutes les opérations POST sont empêchées lorsqu’un verrou **ReadOnly** est configuré pour le compte. L’opération **List Keys** renvoie les clés d’accès au compte, qui peuvent ensuite être utilisées pour lire les données du compte de stockage et y écrire.

Si un client est en possession des clés d’accès au compte au moment où le verrou est appliqué au compte de stockage, ce client peut continuer à utiliser les clés pour accéder aux données. Toutefois, les clients qui n’ont pas accès aux clés doivent utiliser les informations d’identification Azure Active Directory (Azure AD) pour accéder aux données de blob ou de file d’attente dans le compte de stockage.

Si des utilisateurs du portail Azure ont accédé précédemment à des données de blob ou de file d’attente dans le portail avec les clés d’accès au compte, ils peuvent être concernés par l’application d’un verrou **ReadOnly**. Une fois le verrou appliqué, les utilisateurs du portail devront utiliser les informations d’identification Azure AD pour accéder aux données du blob ou de file d’attente dans le portail. Pour ce faire, un utilisateur doit se voir attribuer au moins deux rôles RBAC : le rôle Lecteur Azure Resource Manager au minimum et l’un des rôles d’accès aux données Stockage Azure. Pour plus d’informations, consultez l’un des articles suivants :

- [Choisir comment autoriser l’accès à des données de blobs dans le portail Azure](../blobs/authorize-data-operations-portal.md)
- [Choisir comment autoriser l’accès à des données de files d’attente dans le portail Azure](../queues/authorize-data-operations-portal.md)

Les données dans Azure Files ou du service Table peuvent devenir inaccessibles aux clients qui y accédaient auparavant avec les clés du compte. Si vous devez appliquer un verrou **ReadOnly** à un compte de stockage, la meilleure pratique consiste à déplacer vos charges de travail Azure Files et celles du service Table vers un compte de stockage qui n’est pas verrouillé par un verrou **ReadOnly**.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la protection des données](../blobs/data-protection-overview.md)
- [Verrouiller les ressources pour empêcher des modifications](../../azure-resource-manager/management/lock-resources.md)
