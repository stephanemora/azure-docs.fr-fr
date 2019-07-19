---
title: Gérer l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI | Microsoft Docs
description: Découvrez comment gérer l’accès des utilisateurs, groupes et applications aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC) et d'Azure CLI. Apprenez notamment à lister, à accorder et à supprimer des accès.
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
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bc5deb614e2ac6e47ff3bf241943df92d97699b2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295172"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et d’Azure CLI

Le [contrôle d’accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l’accès aux ressources Azure. Cet article décrit comment gérer l’accès des utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour gérer les accès, il vous faudra l’un des éléments suivants :

* [Bash dans Azure Cloud Shell](/azure/cloud-shell/overview)
* [Interface de ligne de commande Azure](/cli/azure)

## <a name="list-roles"></a>Répertorier les rôles

Pour lister toutes les définitions de rôles disponibles, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list) :

```azurecli
az role definition list
```

L’exemple suivant liste le nom et la description de toutes les définitions de rôles disponibles :

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

L’exemple suivant liste toutes les définitions de rôles intégrées :

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>Lister les définitions de rôle

Pour lister une définition de rôle, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list) :

```azurecli
az role definition list --name <role_name>
```

L’exemple suivant liste la définition de rôle *Contributeur* :

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>Répertorier les actions d'un rôle

L’exemple suivant liste simplement les *actions* et les *notActions* du rôle *Contributeur* :

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

L’exemple suivant liste simplement les actions du rôle *Contributeur de machine virtuelle* :

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Répertorier les accès

Dans le contrôle d’accès en fonction du rôle, vous répertoriez les attributions de rôles pour énumérer les accès.

### <a name="list-role-assignments-for-a-user"></a>Répertorier les attributions de rôles pour un utilisateur

Pour lister les attributions de rôles d’un utilisateur déterminé, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) :

```azurecli
az role assignment list --assignee <assignee>
```

Par défaut, seules les affectations étendues à un abonnement sont affichées. Pour afficher les affectations étendues par ressource ou groupe, utilisez `--all`, et pour afficher les affectations héritées, utilisez `--include-inherited`.

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

### <a name="list-role-assignments-for-a-resource-group"></a>Lister les attributions de rôles pour un groupe de ressources

Pour lister les attributions de rôles qui existent pour un groupe de ressources, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) :

```azurecli
az role assignment list --resource-group <resource_group>
```

L’exemple suivant liste les attributions de rôles du groupe de ressources *pharma-sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Accorder l'accès

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès.

### <a name="create-a-role-assignment-for-a-user"></a>Créer une attribution de rôle pour un utilisateur

Pour créer une attribution de rôle pour un utilisateur dans l’étendue du groupe de ressources, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à l’utilisateur *patlong\@contoso.com* dans l’étendue du groupe de ressources *pharma-sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Créer une affectation de rôle à l’aide de l’ID unique de rôle

Un nom de rôle peut changer dans certaines circonstances, par exemple :

- Vous utilisez vos propres rôles personnalisés et vous décidez d’en modifier le nom.
- Vous utilisez un rôle en préversion dont le nom contient **(préversion)** . Lorsque le rôle est publié, le rôle est renommé.

> [!IMPORTANT]
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Même si un rôle est renommé, l’ID de rôle ne change pas. Si vous utilisez des scripts ou une automatisation pour créer vos attributions de rôles, il est recommandé d’utiliser l’ID de rôle unique au lieu du nom de rôle. Par conséquent, si un rôle est renommé, vos scripts sont plus susceptibles de fonctionner.

Pour créer une attribution de rôle à l’aide de l’ID de rôle unique au lieu du nom de rôle, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *patlong\@contoso.com* dans l’étendue du groupe de ressources *pharma-sales*. Pour obtenir l’ID unique de rôle, vous pouvez utiliser [az role definition list](/cli/azure/role/definition#az-role-definition-list) ou consulter la liste des [rôles intégrés pour les ressources Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Créer une attribution de rôle pour un utilisateur

Pour créer une attribution de rôle pour un groupe, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L’exemple suivant attribue le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue de l’abonnement. Pour obtenir l’ID du groupe, vous pouvez utiliser [az ad group list](/cli/azure/ad/group#az-ad-group-list) ou [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue des ressources d’un réseau virtuel nommé *pharma-sales-project-network* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Créer une attribution de rôle pour une application

Pour créer un rôle pour une application, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à une application associée à l’ID d’objet 44444444-4444-4444-4444-444444444444 dans l’étendue du groupe de ressources *pharma-sales*. Pour obtenir l’ID d’objet de l’application, vous pouvez utiliser [az ad app list](/cli/azure/ad/app#az-ad-app-list) ou [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Suppression d'accès

Dans le contrôle d’accès en fonction du rôle, vous supprimez une attribution de rôle à l’aide de [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) pour supprimer l’accès :

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

L’exemple suivant retire l’attribution de rôle *Collaborateur de machine virtuelle* à l’utilisateur *patlong\@contoso.com* dans le groupe de ressources *pharma-sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

L’exemple suivant retire le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue de l’abonnement. Pour obtenir l’ID du groupe, vous pouvez utiliser [az ad group list](/cli/azure/ad/group#az-ad-group-list) ou [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l’aide d’Azure CLI](tutorial-custom-role-cli.md)
- [Utiliser Azure CLI pour gérer les ressources et les groupes de ressources Azure](../azure-resource-manager/cli-azure-resource-manager.md)
