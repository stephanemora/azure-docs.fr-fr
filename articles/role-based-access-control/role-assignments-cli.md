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
ms.date: 11/21/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 795a97f84bebf6c0e7c1692e82df2f7ce11e0bbd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384094"
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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Lister les attributions de rôle dans l’étendue d’un groupe de ressources

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

### <a name="list-role-assignments-at-a-subscription-scope"></a>Lister les attributions de rôle dans l’étendue d’un abonnement

Pour lister toutes les attributions de rôle dans l’étendue d’un abonnement, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pour obtenir l’ID d’abonnement, accédez au panneau **Abonnements** du portail Azure ou utilisez [az account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Lister les attributions de rôle dans l’étendue d’un groupe d’administration

Pour lister toutes les attributions de rôle dans l’étendue d’un groupe d’administration, utilisez [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pour obtenir l’ID du groupe d’administration, accédez au panneau **Groupes d’administration** dans le portail Azure ou utilisez [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="get-object-ids"></a>Récupérer des ID d’objet

Pour répertorier, ajouter ou supprimer des attributions de rôles, vous devrez peut-être spécifier l’ID unique d’un objet. L’ID a le format : `11111111-1111-1111-1111-111111111111`. Vous pouvez récupérer l’ID à l’aide du Portail Azure ou d’Azure CLI.

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

## <a name="grant-access"></a>Accorder l'accès

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Créer une attribution de rôle pour un utilisateur à une étendue de groupe de ressources

Pour accorder l’accès à un utilisateur dans l’étendue du groupe de ressources, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

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

Pour accorder l’accès à un groupe, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour plus d’informations sur la récupération de l’ID d’objet du groupe, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L’exemple suivant attribue le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue d’un abonnement.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue des ressources d’un réseau virtuel nommé *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Créer une attribution de rôle pour une application dans l’étendue d’un groupe de ressources

Pour octroyer l’accès à une application, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour plus d’informations sur la récupération de l’ID d’objet de l’application, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

L’exemple suivant attribue le rôle *Contributeur de machine virtuelle* à une application associée à l’ID d’objet 44444444-4444-4444-4444-444444444444 dans l’étendue du groupe de ressources *pharma-sales*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Créer une attribution de rôle pour un utilisateur dans l’étendue d’un abonnement

Pour accorder l’accès à un utilisateur dans l’étendue d’un abonnement, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour obtenir l’ID d’abonnement, accédez au panneau **Abonnements** du portail Azure ou utilisez [az account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

L’exemple suivant attribue le rôle *Lecteur* à l’utilisateur *annm\@example.com* dans l’étendue d’un abonnement.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Créer une attribution de rôle pour un utilisateur dans l’étendue d’un groupe d’administration

Pour accorder l’accès à un utilisateur dans l’étendue d’un groupe d’administration, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create). Pour obtenir l’ID du groupe d’administration, accédez au panneau **Groupes d’administration** dans le portail Azure ou utilisez [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

L’exemple suivant attribue le rôle *Lecteur de facturation* à l’utilisateur *alain\@example.com* dans l’étendue d’un groupe d’administration.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Créer une attribution de rôle pour un nouveau principal de service

Dans certains cas, si vous créez un principal de service et que vous tentez immédiatement de lui attribuer un rôle, cette attribution peut échouer. Par exemple, si vous utilisez un script pour créer une identité managée et que vous tentez d’attribuer un rôle à ce principal de service, l’attribution de rôle peut échouer. Cet échec est souvent lié au délai de réplication. Le principal du service est créé dans une région. Toutefois, l’attribution de rôle peut s’effectuer dans une autre région, qui n’a pas encore répliqué le principal de service. Dans le cadre de ce scénario, vous devez spécifier le type de principal lors de la création de l’attribution de rôle.

Pour créer une attribution de rôle, utilisez [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), spécifiez une valeur pour `--assignee-object-id`, puis définissez `--assignee-principal-type` sur `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Dans l’exemple suivant, le rôle *Contributeur de machines virtuelles* est attribué à l’identité managée *msi-test* dans l’étendue du groupe de ressources *pharma-sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
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

L’exemple suivant retire le rôle *Lecteur* au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue d’un abonnement. Pour plus d’informations sur la récupération de l’ID d’objet du groupe, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

L’exemple suivant retire le rôle *Lecteur de facturation* à l’utilisateur *alain\@example.com* dans l’étendue du groupe d’administration. Pour connaître l’ID du groupe d’administration, vous pouvez utiliser [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l’aide d’Azure CLI](tutorial-custom-role-cli.md)
- [Utiliser Azure CLI pour gérer les ressources et les groupes de ressources Azure](../azure-resource-manager/cli-azure-resource-manager.md)
