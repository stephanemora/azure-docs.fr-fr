---
title: Gérer l’accès à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI | Microsoft Docs
description: Découvrez comment gérer l’accès des utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI. Apprenez notamment à lister, à accorder et à supprimer des accès.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6d1e64c7630f3fd35124e6671476174ddfc16bb6
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437097"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>Gérer l’accès à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI

Le [contrôle d’accès en fonction du rôle](overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Cet article décrit comment gérer l’accès des utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour gérer les accès, il vous faudra l’un des éléments suivants :

* [Bash dans Azure Cloud Shell](/azure/cloud-shell/overview)
* [interface de ligne de commande Azure](/cli/azure)

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

### <a name="list-actions-of-a-role"></a>Répertorier les actions d'un rôle

Pour lister les actions d’une définition de rôle, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list) :

```azurecli
az role definition list --name <role_name>
```

L’exemple suivant liste la définition de rôle *Contributeur* :

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
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

L’exemple suivant liste les *actions* et les *notActions* du rôle *Contributeur* :

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

L’exemple suivant liste les actions du rôle *Contributeur de machine virtuelle* :

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

Par défaut, seules sont affichées les attributions étendues à un abonnement. Pour afficher les attributions étendues par ressource ou groupe, utilisez `--all`.

L’exemple suivant liste les attributions de rôles octroyées directement à l’utilisateur *patlong@contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Lister les attributions de rôles pour un groupe de ressources

Pour lister les attributions de rôles qui existent pour un groupe de ressources, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) :

```azurecli
az role assignment list --resource-group <resource_group>
```

L’exemple suivant liste les attributions de rôles du groupe de ressources *pharma-sales-projectforecast* :

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>Accorder l'accès

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès.

### <a name="create-a-role-assignment-for-a-user"></a>Créer une attribution de rôle pour un utilisateur

Pour créer une attribution de rôle pour un utilisateur dans l’étendue du groupe de ressources, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à l’utilisateur *patlong@contoso.com* dans l’étendue du groupe de ressources *pharma-sales-projectforecast* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Créer une attribution de rôle pour un utilisateur

Pour créer une attribution de rôle pour un groupe, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L’exemple suivant attribue le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue de l’abonnement. Pour obtenir l’ID du groupe, vous pouvez utiliser [az ad group list](/cli/azure/ad/group#az-ad-group-list) ou [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue des ressources d’un réseau virtuel nommé *pharma-sales-project-network* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Créer une attribution de rôle pour une application

Pour créer un rôle pour une application, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à une application associée à l’ID d’objet 44444444-4444-4444-4444-444444444444 dans l’étendue du groupe de ressources *pharma-sales-projectforecast*. Pour obtenir l’ID d’objet de l’application, vous pouvez utiliser [az ad app list](/cli/azure/ad/app#az-ad-app-list) ou [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Suppression d'accès

Dans le contrôle d’accès en fonction du rôle, vous supprimez une attribution de rôle à l’aide de [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) pour supprimer l’accès :

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

L’exemple suivant retire l’attribution de rôle *Collaborateur de machine virtuelle* à l’utilisateur *patlong@contoso.com* dans le groupe de ressources *pharma-sales-projectforecast*.

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

L’exemple suivant retire le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue de l’abonnement. Pour obtenir l’ID du groupe, vous pouvez utiliser [az ad group list](/cli/azure/ad/group#az-ad-group-list) ou [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer un rôle personnalisé avec Azure CLI](tutorial-custom-role-cli.md)
- [Utiliser Azure CLI pour gérer les ressources et les groupes de ressources Azure](../azure-resource-manager/xplat-cli-azure-resource-manager.md)