---
title: Attribuer un rôle Azure pour l’accès aux données de table (préversion)
titleSuffix: Azure Storage
description: Découvrez comment attribuer des autorisations pour une table de données (préversion) à un principal de sécurité Azure Active Directory avec un contrôle d’accès en fonction du rôle Azure (Azure RBAC). Le service Stockage Azure prend en charge les rôles personnalisés Azure et ceux intégrés pour l’authentification et l’autorisation via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19c377c1b6d0b5cad7515ca199133f8d10c8742b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760604"
---
# <a name="assign-an-azure-role-for-access-to-table-data-preview"></a>Attribuer un rôle Azure pour l’accès aux données de table (préversion)

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md). Le service Stockage Azure définit des un ensemble de rôles intégrés Azure englobant les ensembles communs d'autorisations qui permettent d'accéder aux données de table dans Stockage Azure (préversion).

Lorsqu’un rôle Azure est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Pour en savoir plus sur l’utilisation d’Azure AD pour autoriser l’accès aux données de table, consultez [Autoriser l’accès aux tables à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> L’autorisation avec Azure AD pour les tables est actuellement **PRÉVERSION**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="assign-an-azure-role"></a>Affecter un rôle Azure

Vous pouvez utiliser PowerShell, Azure CLI ou un modèle Azure Resource Manager pour attribuer un rôle d’accès aux données.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour attribuer un rôle Azure à un principal de sécurité, appelez la commande [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Le format de la commande peut varier selon l’étendue de l’affectation. Pour exécuter la commande, vous devez disposer d’un rôle qui comprend des autorisations **Microsoft.Authorization/roleAssignments/write** qui vous sont assignées au niveau de l’étendue correspondante ou ci-dessus.

Pour attribuer un rôle limité à une table, indiquez une chaîne contenant l’étendue de la table pour le paramètre `--scope`. L’étendue d’une table a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

L’exemple suivant attribue le rôle **Contributeur aux données de la table du stockage** à un utilisateur, limité au niveau de la table. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs :

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Table Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

Pour plus d’informations sur l’attribution de rôles avec PowerShell au niveau de l’abonnement, du groupe de ressources ou de l’étendue du compte de stockage, consultez [Affecter des rôles Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour attribuer un rôle Azure à un principal de sécurité, utilisez la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Le format de la commande peut varier selon l’étendue de l’affectation. Le format de la commande peut varier selon l’étendue de l’affectation. Pour exécuter la commande, vous devez disposer d’un rôle qui comprend des autorisations **Microsoft.Authorization/roleAssignments/write** qui vous sont assignées au niveau de l’étendue correspondante ou ci-dessus.

Pour attribuer un rôle limité à une table, indiquez une chaîne contenant l’étendue de la table pour le paramètre `--scope`. L’étendue d’une table a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

L’exemple suivant affecte le rôle **Contributeur aux données de table de stockage** à un utilisateur, limité au niveau de la table. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs :

```azurecli-interactive
az role assignment create \
    --role "Storage Table Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

Pour plus d’informations sur l’attribution de rôles avec PowerShell au niveau de l’abonnement, du groupe de ressources ou de l’étendue du compte de stockage, consultez [Affecter des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="template"></a>[Modèle](#tab/template)

Pour savoir comment utiliser un modèle d’Azure Resource Manager pour affecter un rôle Azure, consultez [Affecter des rôles Azure à l’aide de modèles Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

---

Gardez à l’esprit les points suivants concernant les attributions de rôles Azure dans Stockage Azure :

- Lorsque vous créez un compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée via Azure AD. Vous devez vous attribuer explicitement un rôle Azure pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, groupe de ressources, compte de stockage, conteneur ou table.
- Si le compte de stockage est verrouillé à l’aide d’un verrou en lecture seule Azure Resource Manager, le verrou empêche l’attribution de rôles Azure étendus au compte de stockage ou à une table.
- La préversion d’Explorateur Stockage dans le Portail Azure ne prend pas en charge l’utilisation d’informations d’identification Azure AD pour visualiser et modifier des données de table. Explorateur Stockage dans le portail Azure utilise toujours les clés de compte pour accéder aux données. Pour utiliser Explorateur Stockage dans le Portail Azure, vous devez être affecté à un rôle qui comprend **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (RBAC Azure) ?](../../role-based-access-control/overview.md)
- [Meilleures pratiques pour Azure RBAC](../../role-based-access-control/best-practices.md)
