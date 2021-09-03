---
title: Attribuer un rôle Azure pour l’accès aux données d’objet blob
titleSuffix: Azure Storage
description: Découvrez comment attribuer des autorisations pour des données blob à un principal de sécurité Azure Active Directory avec le contrôle d’accès en fonction du rôle Azure (RBAC Azure). Le service Stockage Azure prend en charge les rôles personnalisés Azure et ceux intégrés pour l’authentification et l’autorisation via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34c3f19ebb48bef3ece00b6413af2c1d7e585cf5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562742"
---
# <a name="assign-an-azure-role-for-access-to-blob-data"></a>Attribuer un rôle Azure pour l’accès aux données d’objet blob

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md). Le stockage Azure définit un ensemble de rôles intégrés Azure qui englobent les ensembles communs d’autorisations permettant d’accéder aux données blob.

Lorsqu’un rôle Azure est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Pour en savoir plus sur l’utilisation d’Azure AD pour autoriser l’accès aux données blob, consultez [Autoriser l’accès aux objets blob à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md).

> [!NOTE]
> Cet article explique comment attribuer un rôle Azure pour accéder aux données blob d’un compte de stockage. Pour en savoir plus sur l’attribution de rôles pour les opérations de gestion dans Stockage Azure, consultez [Utiliser le fournisseur de ressources Stockage Azure pour accéder aux ressources de gestion](../common/authorization-resource-provider.md).

## <a name="assign-an-azure-role"></a>Affecter un rôle Azure

Vous pouvez utiliser le portail Azure, PowerShell, Azure CLI ou un modèle Azure Resource Manager pour attribuer un rôle d’accès aux données.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour accéder aux données blob dans le portail Azure avec des informations d’identification Azure AD, un utilisateur doit disposer des attributions de rôle suivantes :

- Un rôle d’accès aux données, par exemple **Contributeur aux données blob du stockage**
- Le rôle **Lecteur** d’Azure Resource Manager

Pour savoir comment attribuer ces rôles à un utilisateur, suivez les instructions fournies dans [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).

Le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) est un rôle d’Azure Resource Manager qui permet aux utilisateurs d’afficher les ressources de compte de stockage, mais pas de les modifier. Il ne fournit pas d’autorisations en lecture pour les données dans Stockage Azure, mais uniquement pour les ressources de gestion de compte. Le rôle **Lecteur** est nécessaire pour que les utilisateurs puissent accéder aux conteneurs d’objets blob du portail Azure.

Par exemple, si vous attribuez le rôle **Contributeur aux données Blob du stockage** à l’utilisatrice Mary au niveau d’un conteneur nommé **exemple-container**, puis Mary se voit attribuer un accès en lecture, écriture et suppression à tous les objets blob dans ce conteneur. Toutefois, si Mary souhaite afficher un objet blob dans le portail Azure, le rôle **Contributeur aux données Blob du stockage** ne fournit pas les autorisations suffisantes pour naviguer dans le portail et atteindre l’objet blob dans le but de le consulter. Les autres autorisations sont requises pour naviguer dans le portail et afficher les autres ressources qui y sont visibles.

Un utilisateur doit disposer du rôle **Lecteur** pour utiliser le portail Azure avec les informations d’identification Azure AD. Cela étant, si un utilisateur dispose d’un rôle doté des autorisations **Microsoft.Storage/storageAccounts/listKeys/action**, il peut utiliser le portail avec les clés de compte de stockage, par le biais de l’autorisation de clé partagée. Pour utiliser les clés de compte de stockage, l’accès à la clé partagée doit être autorisé pour le compte de stockage. Pour plus d’informations sur l’autorisation ou l’interdiction de l’accès à la clé partagée, consultez [Empêcher l’autorisation avec clé partagée pour un compte Stockage Azure](../common/shared-key-authorization-prevent.md).

Vous pouvez également attribuer un rôle Azure Resource Manager offrant des autorisations supplémentaires au-delà du rôle **Lecteur**. L’attribution d’autorisations minimales est recommandée en guise de meilleure pratique de sécurité. Pour plus d’informations, consultez [Meilleures pratiques pour Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Avant de vous attribuer un rôle pour l’accès aux données, vous pouvez accéder aux données de votre compte de stockage via le portail Azure, car ce dernier peut également utiliser la clé de compte pour l’accès aux données. Pour plus d’informations, consultez [Choisir comment autoriser l’accès à des données blobs dans le portail Azure](../blobs/authorize-data-operations-portal.md).
>
> La préversion d’Explorateur Stockage dans le portail Azure ne prend pas en charge l’utilisation d’informations d’identification Azure AD pour visualiser et modifier des données blob. Explorateur Stockage dans le portail Azure utilise toujours les clés de compte pour accéder aux données. Pour utiliser Explorateur Stockage dans le portail Azure, un rôle qui comprend **Microsoft.Storage/storageAccounts/listkeys/action** doit vous être attribué.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour attribuer un rôle Azure à un principal de sécurité avec PowerShell, utilisez la commande [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Le format de la commande peut varier selon l’étendue de l’affectation. Pour exécuter la commande, vous devez disposer d’un rôle qui comprend des autorisations **Microsoft.Authorization/roleAssignments/write** qui vous sont assignées au niveau de l’étendue correspondante ou ci-dessus.

Pour affecter un rôle limité à un conteneur, spécifiez une chaîne contenant l’étendue du conteneur pour le paramètre `--scope`. L’étendue d’un conteneur a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L’exemple suivant affecte le rôle **Contributeur aux données Blob du stockage** à un utilisateur, limité à un conteneur nommé *sample-container*. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs : 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

Pour plus d’informations sur l’attribution de rôles avec PowerShell au niveau de l’abonnement, du groupe de ressources ou de l’étendue du compte de stockage, consultez [Affecter des rôles Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour attribuer un rôle Azure à un principal de sécurité avec Azure CLI, utilisez la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Le format de la commande peut varier selon l’étendue de l’affectation. Le format de la commande peut varier selon l’étendue de l’affectation. Pour exécuter la commande, vous devez disposer d’un rôle qui comprend des autorisations **Microsoft.Authorization/roleAssignments/write** qui vous sont assignées au niveau de l’étendue correspondante ou ci-dessus.

Pour affecter un rôle limité à un conteneur, spécifiez une chaîne contenant l’étendue du conteneur pour le paramètre `--scope`. L’étendue d’un conteneur a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L’exemple suivant affecte le rôle **Contributeur aux données Blob du stockage** à un utilisateur, limité au niveau du conteneur. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs :

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

Pour plus d’informations sur l’attribution de rôles avec PowerShell au niveau de l’abonnement, du groupe de ressources ou de l’étendue du compte de stockage, consultez [Affecter des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="template"></a>[Modèle](#tab/template)

Pour savoir comment utiliser un modèle d’Azure Resource Manager pour affecter un rôle Azure, consultez [Affecter des rôles Azure à l’aide de modèles Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

---

Gardez à l’esprit les points suivants concernant les attributions de rôles Azure dans Stockage Azure :

- Lorsque vous créez un compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée via Azure AD. Vous devez vous attribuer explicitement un rôle Azure pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, groupe de ressources, compte de stockage ou conteneur.
- Si le compte de stockage est verrouillé à l’aide d’un verrou en lecture seule Azure Resource Manager, le verrou empêche l’attribution de rôles Azure étendus au compte de stockage ou à un conteneur.
- Si vous avez défini les autorisations nécessaires pour accéder aux données par le biais d’Azure AD et ne parvenez pas à accéder aux données, par exemple, vous obtenez une erreur « AuthorizationPermissionMismatch ». Prévoyez suffisamment de temps pour permettre la réplication des les modifications que vous avez apportées aux autorisations dans Azure AD et assurez-vous de ne pas avoir d’affectations de refus bloquant votre accès. Consultez [Comprendre les affectations de refus Azure](../../role-based-access-control/deny-assignments.md).

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (RBAC Azure) ?](../../role-based-access-control/overview.md)
- [Meilleures pratiques pour Azure RBAC](../../role-based-access-control/best-practices.md)
