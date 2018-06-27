---
title: Gérer l’accès à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI | Microsoft Docs
description: Découvrez comment gérer l’accès des utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI. Cela inclut l’énumération des accès, l’octroi de l’accès et la suppression de l’accès.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 15ff519f5af7471d6adaae44e2af19422ad44fea
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294402"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>Gérer l’accès à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI

Le [contrôle d’accès en fonction du rôle](overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Cet article décrit comment gérer l’accès des utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle et d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour pouvoir gérer les attributions de rôles à partir d’Azure CLI, vous devez satisfaire les prérequis suivants :

* [Azure CLI](/cli/azure). Vous pouvez l’utiliser dans votre navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md) ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux et Windows et l’exécuter à partir de la ligne de commande.

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

## <a name="custom-roles"></a>Rôles personnalisés

### <a name="list-custom-roles"></a>Répertorier les rôles personnalisés

Pour lister les rôles pouvant être affectés dans une étendue, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list).

Les deux exemples suivants listent tous les rôles personnalisés de l’abonnement actuel :

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Pour créer un rôle personnalisé, utilisez [az role definition create](/cli/azure/role/definition#az-role-definition-create). La définition de rôle peut être une description JSON ou le chemin d’un fichier contenant une description JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

L’exemple suivant crée un rôle personnalisé appelé *Virtual Machine Operator*. Ce rôle personnalisé attribue l’accès à toutes les opérations de lecture (« read ») des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network* et attribue l’accès pour démarrer (« start »), redémarrer (« restart ») et surveiller (« monitor ») les machines virtuelles. Ce rôle personnalisé peut être utilisé dans deux abonnements. Cet exemple utilise un fichier JSON en tant qu’entrée.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Mettre à jour un rôle personnalisé

Pour mettre à jour un rôle personnalisé, utilisez d’abord [az role definition list](/cli/azure/role/definition#az-role-definition-list) pour récupérer la définition de rôle. Apportez ensuite les modifications souhaitées à la définition de rôle. Enfin, utilisez [az role definition update](/cli/azure/role/definition#az-role-definition-update) pour enregistrer la définition de rôle mise à jour.

```azurecli
az role definition update --role-definition <role_definition>
```

L’exemple suivant ajoute l’opération *Microsoft.Insights/diagnosticSettings/* aux *Actions* du rôle personnalisé *Virtual Machine Operator*.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Pour spécifier le rôle à supprimer, utilisez le nom ou l’ID du rôle. Pour déterminer l’ID du rôle, utilisez [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

L’exemple suivant supprime le rôle personnalisé *Virtual Machine Operator* :

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

