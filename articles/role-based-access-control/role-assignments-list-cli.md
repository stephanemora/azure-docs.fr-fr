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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b02ec00544ef11ca1048fd6d3bd9bdf3fccd8c8c
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471412"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Lister les attributions de rôles à l’aide du RBAC Azure et d’Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Cet article explique comment lister les attributions de rôles à l’aide d’Azure CLI.

> [!NOTE]
> Si votre organisation possède des fonctions de gestion externalisées pour un fournisseur de services qui utilise la [gestion des ressources déléguées Azure](../lighthouse/concepts/azure-delegated-resource-management.md), les attributions de rôles autorisées par ce fournisseur de services ne seront pas affichées ici.

## <a name="prerequisites"></a>Prérequis

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Répertorier les attributions de rôles pour un utilisateur

Pour lister les attributions de rôles d’un utilisateur déterminé, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) :

```azurecli
az role assignment list --assignee <assignee>
```

Par défaut, seules les attributions de rôles de l'abonnement actuel sont affichées. Pour afficher les attributions de rôles de l’abonnement actuel et antérieur, ajoutez le paramètre `--all`. Pour afficher les attributions de rôles héritées, ajoutez le paramètre `--include-inherited`.

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

Pour lister toutes les attributions de rôle dans l’étendue d’un groupe d’administration, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pour obtenir l’ID du groupe d’administration, accédez au panneau **Groupes d’administration** dans le portail Azure ou utilisez [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Lister les attributions de rôles pour une identité managée

1. Obtenez l’ID d’objet de l’identité managée attribuée par le système ou par l’utilisateur. 

    Pour obtenir l'ID d’objet d'une identité managée attribuée par l'utilisateur, vous pouvez utiliser [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) ou [az identity list](/cli/azure/identity#az-identity-list).

    ```azurecli
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Pour obtenir l'ID d’objet d'une identité managée attribuée par le système, vous pouvez utiliser [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Pour lister les attributions de rôles, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    Par défaut, seules les attributions de rôles de l'abonnement actuel sont affichées. Pour afficher les attributions de rôles de l’abonnement actuel et antérieur, ajoutez le paramètre `--all`. Pour afficher les attributions de rôles héritées, ajoutez le paramètre `--include-inherited`.

    ```azurecli
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et d’Azure CLI](role-assignments-cli.md)
