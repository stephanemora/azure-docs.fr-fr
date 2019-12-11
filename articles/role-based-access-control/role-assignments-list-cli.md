---
title: Lister les attributions de rôles à l’aide du RBAC Azure et d’Azure CLI
description: Découvrez comment déterminer les ressources, utilisateurs, groupes, principaux de service ou identités managées qui ont accès au contrôle d’accès en fonction du rôle (RBAC) Azure et à Azure CLI.
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
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74709893"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Lister les attributions de rôles à l’aide du RBAC Azure et d’Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Cet article explique comment lister les attributions de rôles à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Répertorier les attributions de rôles pour un utilisateur

Pour lister les attributions de rôles d’un utilisateur déterminé, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) :

```azurecli
az role assignment list --assignee <assignee>
```

Par défaut, seules les affectations étendues à un abonnement sont affichées. Pour afficher les attributions étendues par ressource ou groupe, utilisez `--all`, et pour voir les attributions héritées, utilisez `--include-inherited`.

L’exemple suivant liste les attributions de rôles octroyées directement à l’utilisateur *patlong\@contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Lister les attributions de rôles pour un groupe de ressources

Pour lister les attributions de rôle qui existent dans l’étendue d’un groupe de ressources, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) :

```azurecli
az role assignment list --resource-group <resource_group>
```

L’exemple suivant liste les attributions de rôles du groupe de ressources *pharma-sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Répertorier les attributions de rôles pour un abonnement

Pour lister toutes les attributions de rôle dans l’étendue d’un abonnement, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pour obtenir l’ID d’abonnement, accédez au panneau **Abonnements** du portail Azure ou utilisez [az account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Lister les attributions de rôles pour un groupe d’administration

Pour lister toutes les attributions de rôle dans l’étendue d’un groupe d’administration, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pour obtenir l’ID du groupe d’administration, accédez au panneau **Groupes d’administration** dans le portail Azure ou utilisez [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et d’Azure CLI](role-assignments-cli.md)
