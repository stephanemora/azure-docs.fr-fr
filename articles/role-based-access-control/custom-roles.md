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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062169"
---
# <a name="custom-roles-for-azure-resources"></a>Rôles personnalisés pour les ressources Azure

> [!IMPORTANT]
> L’ajout d’un groupe d’administration à `AssignableScopes` est actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si les [rôles intégrés prévus pour les ressources Azure](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Tout comme les rôles intégrés, vous pouvez attribuer des rôles personnalisés à des utilisateurs, à des groupes et à des principaux de service dans des étendues de groupe d’administration, d’abonnement et de groupe de ressources.

Les rôles personnalisés peuvent être partagés entre abonnements qui font confiance au même annuaire Azure AD. Il existe une limite de **5 000** rôles personnalisés par annuaire. (Pour Azure Allemagne et Azure Chine 21Vianet, la limite est de 2 000 rôles personnalisés). Vous pouvez créer des rôles personnalisés à l'aide du portail Azure (préversion), d'Azure PowerShell, d'Azure CLI ou de l'API REST.

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
    "Microsoft.ResourceHealth/availabilityStatuses/read",
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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Quand vous créez un rôle personnalisé, celui-ci s’affiche dans le portail Azure avec une icône de ressource orange.

![Icône de rôle personnalisé](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Procédure de création d’un rôle personnalisé

1. Choisissez la méthode de création de votre choix pour le rôle personnalisé.

    Vous pouvez créer des rôles personnalisés à l'aide du [portail Azure](custom-roles-portal.md) (préversion), d'[Azure PowerShell](custom-roles-powershell.md), d'[Azure CLI](custom-roles-cli.md) ou de l'[API REST](custom-roles-rest.md).

1. Déterminer les autorisations nécessaires

    Lorsque vous créez un rôle personnalisé, vous devez connaître les opérations du fournisseur de ressources qui sont disponibles pour définir vos autorisations. Pour voir la liste des opérations, consultez [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md). Vous ajouterez les opérations aux propriétés `Actions` ou `NotActions` de la [définition de rôle](role-definitions.md). Si vous avez des opérations de données, vous les ajouterez aux propriétés `DataActions` ou `NotDataActions`.

1. Créer le rôle personnalisé

    En règle générale, vous démarrez avec un rôle intégré existant, puis vous le modifiez selon vos besoins. Ensuite, vous utilisez les commandes [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou [az role definition create](/cli/azure/role/definition#az-role-definition-create) pour créer le rôle personnalisé. Pour créer un rôle personnalisé, vous devez disposer de l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur toutes les `AssignableScopes`, comme [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator).

1. Tester le rôle personnalisé

    Une fois que vous avez votre rôle personnalisé, vous devez le tester pour vérifier qu’il fonctionne comme prévu. Si vous avez besoin d’effectuer des ajustements ultérieurement, vous pouvez mettre à jour le rôle personnalisé.

Pour obtenir un tutoriel pas à pas sur la création d’un rôle personnalisé, consultez [Tutoriel : Créer un rôle personnalisé à l’aide d’Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutoriel : Créer un rôle personnalisé avec Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriétés du rôle personnalisé

Un rôle personnalisé dispose des propriétés suivantes.

| Propriété | Obligatoire | Type | Description |
| --- | --- | --- | --- |
| `Name` | Oui | String | Nom complet du rôle personnalisé. Si une définition de rôle est une ressource de niveau groupe d'administration ou abonnement, elle peut cependant être utilisée dans plusieurs abonnements partageant le même annuaire Azure AD. Ce nom d’affichage doit être unique dans l’étendue de l’annuaire Azure AD. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 128. |
| `Id` | Oui | String | ID unique du rôle personnalisé. Pour Azure PowerShell et Azure CLI, cet ID est généré automatiquement lorsque vous créez un nouveau rôle. |
| `IsCustom` | Oui | String | Indique s’il s’agit d’un rôle personnalisé. À définir sur `true` pour les rôles personnalisés. |
| `Description` | Oui | String | Description du rôle personnalisé. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 1 024. |
| `Actions` | Oui | String[] | Tableau de chaînes qui spécifie les opérations d’administration que le rôle autorise. Pour plus d’informations, voir [Actions](role-definitions.md#actions). |
| `NotActions` | Non | String[] | Tableau de chaînes qui spécifie les opérations d’administration exclues des `Actions` autorisées. Pour plus d’informations, voir [NotActions](role-definitions.md#notactions). |
| `DataActions` | Non | String[] | Tableau de chaînes qui spécifie les opérations de données que le rôle autorise sur vos données au sein de cet objet. Si vous créez un rôle personnalisé avec `DataActions`, ce rôle ne peut pas être affecté au niveau de l’étendue du groupe d’administration. Pour plus d’informations, consultez [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Non | String[] | Tableau de chaînes qui spécifie les opérations de données exclues des `DataActions` autorisées. Pour plus d’informations, consultez [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Oui | String[] | Tableau de chaînes qui spécifie les étendues pour lesquelles le rôle personnalisé est disponible à des fins d’attribution. Vous ne pouvez définir qu’un seul groupe d’administration dans `AssignableScopes` d’un rôle personnalisé. L’ajout d’un groupe d’administration à `AssignableScopes` est actuellement en préversion. Pour plus d’informations, voir [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Qui peut créer, supprimer, mettre à jour ou afficher un rôle personnalisé

Tout comme pour les rôles intégrés, la propriété `AssignableScopes` spécifie les étendues pour lesquelles le rôle est disponible à des fins d’attribution. La propriété `AssignableScopes` pour un rôle personnalisé contrôle également qui peut créer, supprimer, mettre à jour ou afficher le rôle personnalisé.

| Tâche | Opération | Description |
| --- | --- | --- |
| Créer/supprimer un rôle personnalisé | `Microsoft.Authorization/ roleDefinitions/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent créer (ou supprimer) des rôles personnalisés utilisables dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) des groupe d’administration, des abonnements et des groupes de ressources. |
| Mettre à jour un rôle personnalisé | `Microsoft.Authorization/ roleDefinitions/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent mettre à jour des rôles personnalisés dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) des groupe d’administration, des abonnements et des groupes de ressources. |
| Afficher un rôle personnalisé | `Microsoft.Authorization/ roleDefinitions/read` | Les utilisateurs ayant accès à cette opération dans une étendue peuvent afficher les rôles personnalisés disponibles pour attribution dans cette étendue. Tous les rôles intégrés permettent que les rôles personnalisés soient disponibles pour attribution. |

## <a name="custom-role-limits"></a>Limites des rôles personnalisés

La liste suivante décrit les limites des rôles personnalisés.

- Chaque annuaire peut avoir jusqu’à **5 000** rôles personnalisés.
- Azure Allemagne et Azure Chine 21Vianet peuvent avoir jusqu’à 2 000 rôles personnalisés pour chaque annuaire.
- Vous ne pouvez pas définir `AssignableScopes` à l’étendue racine (`"/"`).
- Vous ne pouvez définir qu’un seul groupe d’administration dans `AssignableScopes` d’un rôle personnalisé. L’ajout d’un groupe d’administration à `AssignableScopes` est actuellement en préversion.
- Les rôles personnalisés avec `DataActions` ne peuvent pas être attribués dans l’étendue du groupe d’administration.
- Azure Resource Manager ne valide pas le groupe d’administration existant dans l’étendue attribuable de la définition de rôle.

Pour plus d’informations sur les rôles personnalisés et les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Étapes suivantes
- [Créer ou mettre à jour des rôles personnalisés Azure à l'aide du portail Azure (préversion)](custom-roles-portal.md)
- [Comprendre les définitions de rôle relatives aux ressources Azure](role-definitions.md)
- [Résoudre des problèmes liés au contrôle d'accès en fonction du rôle pour les ressources Azure](troubleshooting.md)
