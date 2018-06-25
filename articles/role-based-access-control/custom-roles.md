---
title: Créer des rôles personnalisés dans RBAC Azure | Microsoft Docs
description: Découvrez comment définir des rôles personnalisés à l’aide du contrôle d’accès en fonction du rôle Azure pour une gestion plus précise des identités dans votre abonnement Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3baf616e448f1f6d5292161ae125502d72141940
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266592"
---
# <a name="create-custom-roles-in-azure"></a>Créer des rôles personnalisés dans Azure

Si les [rôles intégrés](built-in-roles.md) ne répondent pas à vos besoins d’accès, vous pouvez créer vos propres rôles personnalisés. Comme avec les rôles intégrés, vous pouvez affecter des rôles personnalisés à des utilisateurs, des groupes et des principaux de service dans l’étendue des abonnements, des groupes de ressources et des ressources. Les rôles personnalisés sont stockés dans un locataire Azure Active Directory (Azure AD) et peuvent être partagés entre les abonnements. Chaque locataire peut avoir jusqu’à 2000 rôles personnalisés. Vous pouvez créer des rôles personnalisés à l’aide d’Azure PowerShell, de l’interface de ligne de commande Azure et de l’API REST.

Cet article décrit un exemple qui initie à la création de rôles personnalisés à l’aide de PowerShell et de l’interface de ligne de commande Azure.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Créer un rôle personnalisé pour ouvrir des demandes de support à l’aide de PowerShell

Pour créer un rôle personnalisé, vous pouvez prendre un rôle prédéfini pour commencer, le modifier puis créer un nouveau rôle. Pour cet exemple, le rôle intégré [Lecteur](built-in-roles.md#reader) est personnalisé pour créer un rôle personnalisé nommé « Reader support tickets access level ». Il permet à l’utilisateur de consulter tous les éléments de l’abonnement, ainsi que d’ouvrir des demandes de support.

> [!NOTE]
> Les deux seuls rôles intégrés qui autorisent un utilisateur à ouvrir des demandes de support sont [Propriétaire](built-in-roles.md#owner) et [Contributeur](built-in-roles.md#contributor). Pour pouvoir ouvrir des demandes de support, un utilisateur doit avoir un rôle au niveau de l’étendue de l’abonnement, car toutes les demandes de prise en charge sont créées sur la base d’un abonnement Azure.

Dans PowerShell, utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour exporter le rôle [Lecteur](built-in-roles.md#reader) au format JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Voici la sortie JSON pour le rôle [Lecteur](built-in-roles.md#reader). Un rôle est généralement composé de trois sections principales : `Actions`, `NotActions` et `AssignableScopes`. Dans la section `Actions`, toutes les opérations autorisées pour ce rôle sont répertoriées. Pour exclure des opérations d’`Actions`, vous les ajoutez à `NotActions`. Vous calculez les autorisations effectives en soustrayant les opérations `NotActions` des opérations `Actions`.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Vous modifiez ensuite la sortie JSON pour créer votre rôle personnalisé. Dans ce cas, pour créer des tickets de support, l’opération `Microsoft.Support/*` doit être ajoutée. Chaque action est mise à disposition à partir d’un fournisseur de ressources. Pour obtenir une liste des opérations pour un fournisseur de ressources, vous pouvez utiliser la commande [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) ou consulter [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md).

Enfin, le rôle doit obligatoirement contenir les ID d’abonnement explicites où il est utilisé. Les ID d’abonnement sont répertoriés sous la section `AssignableScopes`. Sans ceux-ci, vous ne serez pas autorisé à importer le rôle dans votre abonnement.

Enfin, vous devez définir la propriété `IsCustom` sur `true` pour spécifier qu’il s’agit d’un rôle personnalisé.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Pour créer le rôle personnalisé, vous utilisez la commande [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) et fournissez le fichier de définition de rôle JSON mis à jour.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Une fois la commande [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) exécutée, le nouveau rôle personnalisé est disponible dans le portail Azure et peut être attribué aux utilisateurs.

![capture d’écran d’un rôle personnalisé importé dans le portail Azure](./media/custom-roles/18.png)

![capture d’écran de l’attribution d’un rôle importé personnalisé à un utilisateur dans le même annuaire](./media/custom-roles/19.png)

![capture d’écran d’autorisations pour un rôle importé personnalisé](./media/custom-roles/20.png)

Les utilisateurs auxquels ce rôle personnalisé est attribué peuvent créer des demandes de support.

![capture d’écran d’un rôle personnalisé créant des demandes de support](./media/custom-roles/21.png)

Les utilisateurs auxquels ce rôle personnalisé est attribué ne peuvent pas effectuer d’autres actions, telles que créer des machines virtuelles ou créer des groupes de ressources.

![capture d’écran d’un rôle personnalisé ne pouvant pas créer de machines virtuelles](./media/custom-roles/22.png)

![capture d’écran d’un rôle personnalisé ne pouvant pas créer de nouveaux groupes de ressources](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Créer un rôle personnalisé pour ouvrir des demandes de support à l’aide d’Azure CLI

Les étapes pour créer un rôle personnalisé à l’aide d’Azure CLI sont similaires à celles de PowerShell, à l’exception la sortie JSON qui est différente.

Pour cet exemple, vous pouvez prendre le rôle intégré [Lecteur](built-in-roles.md#reader) pour commencer. Pour répertorier les actions du rôle [Lecteur](built-in-roles.md#reader), utilisez la commande [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Créez un fichier JSON au format suivant. L’opération `Microsoft.Support/*` a été ajoutée dans la section `Actions` afin que cet utilisateur puisse ouvrir des demandes de support tout en continuant à être lecteur. Vous devez ajouter dans la section `AssignableScopes` l’ID de l’abonnement dans lequel ce rôle sera utilisé.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Pour créer le rôle personnalisé, utilisez la commande [az role definition create](/cli/azure/role/definition#az_role_definition_create).

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Le nouveau rôle personnalisé est désormais disponible dans le portail Azure, et le processus d’attribution est le même que dans la section PowerShell précédente.

![Capture d’écran du portail Azure montrant le rôle personnalisé créé à l’aide d’Azure CLI 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Voir aussi
- [Comprendre les définitions de rôles](role-definitions.md)
- [Gestion du contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](role-assignments-powershell.md)
- [Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure](role-assignments-cli.md)
- [Gérer le contrôle d’accès en fonction du rôle à l’aide de l’API REST](role-assignments-rest.md)
