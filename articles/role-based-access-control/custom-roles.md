---
title: Rôles personnalisés dans Azure | Microsoft Docs
description: Découvrez comment définir des rôles personnalisés avec le contrôle d’accès en fonction du rôle (RBAC) d’Azure pour une gestion affinée des accès aux ressources dans Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7554ef46289600cd15e4675a91f42a2cd735f18
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112659"
---
# <a name="custom-roles-in-azure"></a>Rôles personnalisés dans Azure

Si les [rôles intégrés](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Comme avec les rôles intégrés, vous pouvez affecter des rôles personnalisés à des utilisateurs, des groupes et des principaux de service dans l’étendue des abonnements, des groupes de ressources et des ressources. Les rôles personnalisés sont stockés dans un locataire Azure Active Directory (Azure AD) et peuvent être partagés entre les abonnements. Chaque locataire peut avoir jusqu’à 2000 rôles personnalisés. Vous pouvez créer des rôles personnalisés à l’aide d’Azure PowerShell, de l’interface de ligne de commande Azure et de l’API REST.

## <a name="custom-role-example"></a>Exemple de rôle personnalisé

L’exemple suivant présente un rôle personnalisé pour la surveillance et le redémarrage de machines virtuelles, tel qu’affiché à l’aide d’Azure PowerShell :

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

Une fois que vous avez créé un rôle personnalisé, celui-ci s’affiche dans le portail Azure avec une icône de ressource orange.

![Icône de rôle personnalisé](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Procédure de création d’un rôle personnalisé

1. Déterminer les autorisations nécessaires

    Lorsque vous créez un rôle personnalisé, vous devez connaître les opérations du fournisseur de ressources qui sont disponibles pour définir vos autorisations. Pour afficher la liste des opérations, vous pouvez utiliser les commandes [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).
    Pour spécifier les autorisations pour votre rôle personnalisé, vous ajoutez les opérations à la propriété `Actions` ou `NotActions` de la [définition du rôle](role-definitions.md). Si vous avez des opérations de données, ajoutez-les à la propriété `DataActions` ou `NotDataActions`.

2. Créer le rôle personnalisé

    Vous pouvez utiliser Azure PowerShell ou Azure CLI pour créer le rôle personnalisé. En règle générale, vous démarrez avec un rôle intégré existant, puis vous le modifiez selon vos besoins. Ensuite, vous utilisez les commandes [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) ou [az role definition create](/cli/azure/role/definition#az-role-definition-create) pour créer le rôle personnalisé. Pour créer un rôle personnalisé, vous devez disposer de l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur toutes les `AssignableScopes`, comme [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator).

3. Tester le rôle personnalisé

    Une fois que vous avez votre rôle personnalisé, vous devez le tester pour vérifier qu’il fonctionne comme prévu. Si des ajustements sont nécessaires, vous pouvez mettre à jour le rôle personnalisé.

## <a name="custom-role-properties"></a>Propriétés du rôle personnalisé

Un rôle personnalisé dispose des propriétés suivantes.

| Propriété | Obligatoire | Type | Description |
| --- | --- | --- | --- |
| `Name` | Oui | Chaîne | Nom complet du rôle personnalisé. Doit être unique à votre locataire. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 128. |
| `Id` | Oui | Chaîne | ID unique du rôle personnalisé. Pour Azure PowerShell et Azure CLI, cet ID est généré automatiquement lorsque vous créez un nouveau rôle. |
| `IsCustom` | Oui | Chaîne | Indique s’il s’agit d’un rôle personnalisé. À définir sur `true` pour les rôles personnalisés. |
| `Description` | Oui | Chaîne | Description du rôle personnalisé. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 1 024. |
| `Actions` | Oui | String[] | Tableau de chaînes qui spécifie les opérations d’administration que le rôle autorise. Pour plus d’informations, voir [Actions](role-definitions.md#actions). |
| `NotActions` | Non  | String[] | Tableau de chaînes qui spécifie les opérations d’administration exclues des `Actions` autorisées. Pour plus d’informations, voir [NotActions](role-definitions.md#notactions). |
| `DataActions` | Non  | String[] | Tableau de chaînes qui spécifie les opérations de données que le rôle autorise sur vos données au sein de cet objet. Pour plus d’informations, voir [DataActions (préversion)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Non  | String[] | Tableau de chaînes qui spécifie les opérations de données exclues des `DataActions` autorisées. Pour plus d’informations, voir [NotDataActions (préversion)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Oui | String[] | Tableau de chaînes qui spécifie les étendues pour lesquelles le rôle personnalisé est disponible à des fins d’attribution. Impossible de définir sur l’étendue racine (`"/"`). Pour plus d’informations, voir [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>AssignableScopes pour rôles personnalisés

Tout comme pour les rôles intégrés, la propriété `AssignableScopes` spécifie les étendues pour lesquelles le rôle est disponible à des fins d’attribution. Toutefois, vous ne pouvez pas utiliser l’étendue racine (`"/"`) dans vos propres rôles personnalisés. Si vous essayez, vous obtiendrez une erreur d’autorisation. La propriété `AssignableScopes` pour un rôle personnalisé contrôle également qui peut créer, supprimer, modifier ou afficher le rôle personnalisé.

| Tâche | Opération | Description |
| --- | --- | --- |
| Créer/supprimer un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent créer (ou supprimer) des rôles personnalisés utilisables dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) d’abonnements, de groupes de ressources et de ressources. |
| Modifier un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent modifier des rôles personnalisés dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) d’abonnements, de groupes de ressources et de ressources. |
| Afficher un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/read` | Les utilisateurs ayant accès à cette opération dans une étendue peuvent afficher les rôles personnalisés disponibles pour attribution dans cette étendue. Tous les rôles intégrés permettent que les rôles personnalisés soient disponibles pour attribution. |

## <a name="next-steps"></a>Étapes suivantes
- [Créer des rôles personnalisés à l’aide d’Azure PowerShell](custom-roles-powershell.md)
- [Créer des rôles personnalisés à l’aide d’Azure CLI](custom-roles-cli.md)
- [Comprendre les définitions de rôles](role-definitions.md)
