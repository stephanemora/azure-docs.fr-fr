---
title: Rôles personnalisés pour les ressources Azure | Microsoft Docs
description: Découvrez comment créerdes rôles personnalisés avec le contrôle d’accès en fonction du rôle (RBAC) pour une gestion affinée des accès aux ressources Azure.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6307c57f32700c0c2dd2e5da15b98a2a54dbe9c4
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339326"
---
# <a name="custom-roles-for-azure-resources"></a>Rôles personnalisés pour les ressources Azure

Si les [rôles intégrés pour les ressources Azure](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Comme avec les rôles intégrés, vous pouvez affecter des rôles personnalisés à des utilisateurs, des groupes et des principaux de service dans l’étendue des abonnements, des groupes de ressources et des ressources. Les rôles personnalisés sont stockés dans un annuaire Azure Active Directory et peuvent être partagés entre des abonnements. Chaque annuaire peut avoir jusqu’à 2 000 rôles personnalisés. Vous pouvez créer des rôles personnalisés à l’aide d’Azure PowerShell, de l’interface de ligne de commande Azure et de l’API REST.

## <a name="custom-role-example"></a>Exemple de rôle personnalisé

Voici ce à quoi ressemble un rôle personnalisé tel qu’il apparaît au format JSON. Ce rôle personnalisé peut être utilisé pour surveiller et redémarrer des machines virtuelles.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Quand vous créez un rôle personnalisé, celui-ci s’affiche dans le portail Azure avec une icône de ressource orange.

![Icône de rôle personnalisé](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Procédure de création d’un rôle personnalisé

1. Déterminer les autorisations nécessaires

    Lorsque vous créez un rôle personnalisé, vous devez connaître les opérations du fournisseur de ressources qui sont disponibles pour définir vos autorisations. Pour afficher la liste des opérations, vous pouvez utiliser les commandes [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).
    Pour spécifier les autorisations pour votre rôle personnalisé, vous ajoutez les opérations à la propriété `Actions` ou `NotActions` de la [définition du rôle](role-definitions.md). Si vous avez des opérations de données, ajoutez-les à la propriété `DataActions` ou `NotDataActions`.

2. Créer le rôle personnalisé

    Vous pouvez utiliser Azure PowerShell ou Azure CLI pour créer le rôle personnalisé. En règle générale, vous démarrez avec un rôle intégré existant, puis vous le modifiez selon vos besoins. Ensuite, vous utilisez les commandes [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou [az role definition create](/cli/azure/role/definition#az-role-definition-create) pour créer le rôle personnalisé. Pour créer un rôle personnalisé, vous devez disposer de l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur toutes les `AssignableScopes`, comme [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator).

3. Tester le rôle personnalisé

    Une fois que vous avez votre rôle personnalisé, vous devez le tester pour vérifier qu’il fonctionne comme prévu. Si vous avez besoin d’effectuer des ajustements ultérieurement, vous pouvez mettre à jour le rôle personnalisé.

Pour obtenir un tutoriel pas à pas sur la création d’un rôle personnalisé, consultez [Tutoriel : Créer un rôle personnalisé à l’aide d’Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutoriel : Créer un rôle personnalisé avec Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriétés du rôle personnalisé

Un rôle personnalisé dispose des propriétés suivantes.

| Propriété | Obligatoire | Type | Description |
| --- | --- | --- | --- |
| `Name` | OUI | Chaîne | Nom complet du rôle personnalisé. Si une définition de rôle est une ressource de niveau abonnement, elle peut cependant être utilisée dans plusieurs abonnements partageant le même annuaire Azure AD. Ce nom d’affichage doit être unique dans l’étendue de l’annuaire Azure AD. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 128. |
| `Id` | OUI | Chaîne | ID unique du rôle personnalisé. Pour Azure PowerShell et Azure CLI, cet ID est généré automatiquement lorsque vous créez un nouveau rôle. |
| `IsCustom` | OUI | Chaîne | Indique s’il s’agit d’un rôle personnalisé. À définir sur `true` pour les rôles personnalisés. |
| `Description` | OUI | Chaîne | Description du rôle personnalisé. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 1 024. |
| `Actions` | OUI | String[] | Tableau de chaînes qui spécifie les opérations d’administration que le rôle autorise. Pour plus d’informations, voir [Actions](role-definitions.md#actions). |
| `NotActions` | Non  | String[] | Tableau de chaînes qui spécifie les opérations d’administration exclues des `Actions` autorisées. Pour plus d’informations, voir [NotActions](role-definitions.md#notactions). |
| `DataActions` | Non  | String[] | Tableau de chaînes qui spécifie les opérations de données que le rôle autorise sur vos données au sein de cet objet. Pour plus d’informations, voir [DataActions (préversion)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Non  | String[] | Tableau de chaînes qui spécifie les opérations de données exclues des `DataActions` autorisées. Pour plus d’informations, voir [NotDataActions (préversion)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | OUI | String[] | Tableau de chaînes qui spécifie les étendues pour lesquelles le rôle personnalisé est disponible à des fins d’attribution. Ne peut pas être défini sur l’étendue racine (`"/"`) ou sur une étendue de groupe d’administration. Pour plus d’informations, consultez [AssignableScopes](role-definitions.md#assignablescopes) et [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Qui peut créer, supprimer, mettre à jour ou afficher un rôle personnalisé

Tout comme pour les rôles intégrés, la propriété `AssignableScopes` spécifie les étendues pour lesquelles le rôle est disponible à des fins d’attribution. La propriété `AssignableScopes` pour un rôle personnalisé contrôle également qui peut créer, supprimer, mettre à jour ou afficher le rôle personnalisé.

| Tâche | Opération | Description |
| --- | --- | --- |
| Créer/supprimer un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent créer (ou supprimer) des rôles personnalisés utilisables dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) d’abonnements, de groupes de ressources et de ressources. |
| Mettre à jour un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent mettre à jour des rôles personnalisés dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) d’abonnements, de groupes de ressources et de ressources. |
| Afficher un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/read` | Les utilisateurs ayant accès à cette opération dans une étendue peuvent afficher les rôles personnalisés disponibles pour attribution dans cette étendue. Tous les rôles intégrés permettent que les rôles personnalisés soient disponibles pour attribution. |

## <a name="next-steps"></a>Étapes suivantes
- [Créer des rôles personnalisés pour les ressources Azure à l’aide d’Azure PowerShell](custom-roles-powershell.md)
- [Créer des rôles personnalisés pour les ressources Azure à l’aide d’Azure CLI](custom-roles-cli.md)
- [Comprendre les définitions de rôle relatives aux ressources Azure](role-definitions.md)
- [Résoudre des problèmes liés au contrôle d'accès en fonction du rôle pour les ressources Azure](troubleshooting.md)
