---
title: Ajouter ou supprimer des attributions de rôles avec le RBAC Azure et Azure CLI
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure et d’Azure CLI.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0351721283df68fde910ae16b16d567954c3e6fb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707892"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Ajouter ou supprimer des attributions de rôles avec le RBAC Azure et Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Cet article explique comment attribuer des rôles avec Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour ajouter ou supprimer des attributions de rôles, vous devez disposer :

- d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)
- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Récupérer des ID d’objet

Pour ajouter ou supprimer des attributions de rôles, vous devrez peut-être spécifier l’ID unique d’un objet. L’ID a le format : `11111111-1111-1111-1111-111111111111`. Vous pouvez récupérer l’ID à l’aide du Portail Azure ou d’Azure CLI.

### <a name="user"></a>Utilisateur

Pour récupérer l’ID d’objet pour un utilisateur Azure AD, vous pouvez utiliser [az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Groupe

Pour obtenir l’ID d’objet d’un groupe Azure AD, vous pouvez utiliser [az ad group show](/cli/azure/ad/group#az-ad-group-show) ou [az ad group list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Application

Pour récupérer l’ID objet d’un principal de service Azure AD (identité utilisée par une application), vous pouvez utiliser [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list). Pour un principal de service, utilisez l’ID d’objet et **non** l’ID d’application.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Dans le contrôle RBAC, vous ajoutez une attribution de rôle pour accorder l’accès.

### <a name="user-at-a-resource-group-scope"></a>Utilisateur dans l’étendue d’un groupe de ressources

Pour ajouter une attribution de rôle pour un utilisateur dans l’étendue d’un groupe de ressources, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à l’utilisateur *patlong\@contoso.com* dans l’étendue du groupe de ressources *pharma-sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Utilisation de l’ID de rôle unique

Un nom de rôle peut changer dans certaines circonstances, par exemple :

- Vous utilisez vos propres rôles personnalisés et vous décidez d’en modifier le nom.
- Vous utilisez un rôle en préversion dont le nom contient **(préversion)** . Lorsque le rôle est publié, le rôle est renommé.

> [!IMPORTANT]
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Même si un rôle est renommé, l’ID de rôle ne change pas. Si vous utilisez des scripts ou une automatisation pour créer vos attributions de rôles, il est recommandé d’utiliser l’ID de rôle unique au lieu du nom de rôle. Par conséquent, si un rôle est renommé, vos scripts sont plus susceptibles de fonctionner.

Pour ajouter une attribution de rôle en utilisant l’ID de rôle unique au lieu du nom de rôle, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *patlong\@contoso.com* dans l’étendue du groupe de ressources *pharma-sales*. Pour obtenir l’ID unique de rôle, vous pouvez utiliser [az role definition list](/cli/azure/role/definition#az-role-definition-list) ou consulter la liste des [rôles intégrés pour les ressources Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Groupe dans l’étendue d’un abonnement

Pour ajouter une attribution de rôle pour un groupe, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour plus d’informations sur la récupération de l’ID d’objet du groupe, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L’exemple suivant attribue le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue d’un abonnement.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Groupe dans l’étendue d’une ressource

Pour ajouter une attribution de rôle pour un groupe, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour plus d’informations sur la récupération de l’ID d’objet du groupe, consultez [Récupérer des ID d’objet](#get-object-ids).

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue des ressources d’un réseau virtuel nommé *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Application dans l’étendue d’un groupe de ressources

Pour ajouter une attribution de rôle pour une application, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour plus d’informations sur la récupération de l’ID d’objet de l’application, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à une application associée à l’ID d’objet 44444444-4444-4444-4444-444444444444 dans l’étendue du groupe de ressources *pharma-sales*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Utilisateur dans l’étendue d’un abonnement

Pour ajouter une attribution de rôle pour un utilisateur dans l’étendue d’un abonnement, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour obtenir l’ID d’abonnement, accédez au panneau **Abonnements** du portail Azure ou utilisez [az account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

L’exemple suivant attribue le rôle *Lecteur* à l’utilisateur *annm\@example.com* dans l’étendue d’un abonnement.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Utilisateur dans l’étendue d’un groupe d’administration

Pour ajouter une attribution de rôle pour un utilisateur dans l’étendue d’un groupe d’administration, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour obtenir l’ID du groupe d’administration, accédez au panneau **Groupes d’administration** dans le portail Azure ou utilisez [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

L’exemple suivant attribue le rôle *Lecteur de facturation* à l’utilisateur *alain\@example.com* dans l’étendue d’un groupe d’administration.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nouveau principal de service

Dans certains cas, si vous créez un principal de service et que vous tentez immédiatement de lui attribuer un rôle, cette attribution peut échouer. Par exemple, si vous utilisez un script pour créer une identité managée et que vous tentez d’attribuer un rôle à ce principal de service, l’attribution de rôle peut échouer. Cet échec est souvent lié au délai de réplication. Le principal du service est créé dans une région. Toutefois, l’attribution de rôle peut s’effectuer dans une autre région, qui n’a pas encore répliqué le principal de service. Dans le cadre de ce scénario, vous devez spécifier le type de principal lors de la création de l’attribution de rôle.

Pour ajouter une attribution de rôle, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), spécifiez une valeur pour `--assignee-object-id`, puis définissez `--assignee-principal-type` sur `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Dans l’exemple suivant, le rôle *Contributeur de machines virtuelles* est attribué à l’identité managée *msi-test* dans l’étendue du groupe de ressources *pharma-sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Dans le contrôle d’accès en fonction du rôle, vous supprimez une attribution de rôle à l’aide de [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) pour supprimer l’accès :

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

L’exemple suivant retire l’attribution de rôle *Collaborateur de machine virtuelle* à l’utilisateur *patlong\@contoso.com* dans le groupe de ressources *pharma-sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

L’exemple suivant retire le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue d’un abonnement. Pour plus d’informations sur la récupération de l’ID d’objet du groupe, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

L’exemple suivant retire le rôle *Lecteur de facturation* à l’utilisateur *alain\@example.com* dans l’étendue du groupe d’administration. Pour connaître l’ID du groupe d’administration, vous pouvez utiliser [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Étapes suivantes

- [Lister les attributions de rôles à l’aide du RBAC Azure et d’Azure CLI](role-assignments-list-cli.md)
- [Utiliser Azure CLI pour gérer les ressources et les groupes de ressources Azure](../azure-resource-manager/cli-azure-resource-manager.md)
