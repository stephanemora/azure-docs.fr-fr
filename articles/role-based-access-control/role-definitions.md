---
title: Comprendre les définitions de rôles dans le RBAC Azure | Microsoft Docs
description: Apprenez-en davantage sur les définitions de rôles dans le cadre du contrôle d’accès en fonction du rôle (RBAC), et sur la manière de définir des rôles personnalisés pour une gestion affinée des accès aux ressources dans Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161812"
---
# <a name="understand-role-definitions"></a>Comprendre les définitions de rôles

Si vous essayez de comprendre comment un rôle fonctionne, ou si vous créez votre propre [rôle personnalisé](custom-roles.md), il est utile de comprendre la façon dont les rôles sont définis. Cet article décrit en détail les définitions de rôles et fournit quelques exemples.

## <a name="role-definition-structure"></a>Structure d’une définition de rôle

Une *définition de rôle* est une collection d’autorisations. On l’appelle parfois simplement *rôle*. Une définition de rôle répertorie les opérations qui peuvent être effectuées, telles que lire, écrire et supprimer. Il peut également répertorier les opérations qui ne peut pas être effectuées. Une définition de rôle présente la structure suivante :

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Les opérations sont spécifiées à l’aide de chaînes dont le format est le suivant :

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

La portion `{action}` d’une chaîne d’opération spécifie le type des opérations que vous pouvez effectuer sur un type de ressource. Par exemple, vous verrez les sous-chaînes suivantes dans `{action}` :

| Sous-chaîne d’action    | Description         |
| ------------------- | ------------------- |
| `*` | Le caractère générique donne accès à toutes les opérations qui correspondent à la chaîne. |
| `read` | Permet les opérations de lecture (GET). |
| `write` | Permet les opérations d’écriture (PUT, POST et PATCH). |
| `delete` | Permet les opérations de suppression (DELETE). |

Voici la définition du rôle [Contributeur](built-in-roles.md#contributor) au format JSON. L’opération de caractère générique (`*`) sous `actions` indique que le principal affecté à ce rôle peut effectuer toutes les actions, ou, en d’autres termes, tout gérer. Cela inclut les actions qui seront définies dans le futur, à mesure qu’Azure ajoutera de nouveaux types de ressources. Les opérations sous `notActions` sont soustraites de `actions`. Dans le cas du rôle [Contributeur](built-in-roles.md#contributor), `notActions` supprime la possibilité pour ce rôle de gérer et d’autoriser l’accès aux ressources.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Opérations de gestion

Le contrôle d’accès en fonction du rôle pour les opérations de gestion est spécifié dans les sections `actions` et `notActions` d’une définition de rôle. Voici quelques exemples d’opérations de gestion dans Azure :

- Gérer l’accès à un compte de stockage
- Créer, mettre à jour ou supprimer un conteneur d’objets blob
- Supprimer un groupe de ressources et toutes ses ressources

Vos données n’héritent pas de l’accès à la gestion. Cette séparation empêche des rôles avec caractères génériques (`*`) d’avoir un accès illimité à vos données. Par exemple, si un utilisateur a un rôle [Lecteur](built-in-roles.md#reader) sur un abonnement, il peut afficher le compte de stockage, mais pas les données sous-jacentes.

## <a name="actions"></a>actions

L’autorisation `actions` spécifie les opérations de gestion auxquelles le rôle donne accès. Il s’agit d’un ensemble de chaînes d’opération qui identifient les opérations sécurisables des fournisseurs de ressources Azure. Voici quelques exemples d’opérations de gestion qui peuvent être utilisées dans `actions`.

| Chaîne d’opération    | Description         |
| ------------------- | ------------------- |
| `*/read` | Accorde l’accès aux opérations de lecture pour tous les types de ressources de l’ensemble des fournisseurs de ressources Azure.|
| `Microsoft.Compute/*` | Accorde l’accès à l’ensemble des opérations pour tous les types de ressources dans le fournisseur de ressources Microsoft.Compute.|
| `Microsoft.Network/*/read` | Accorde l’accès aux opérations de lecture pour tous les types de ressources dans le fournisseur de ressources Microsoft.Network d’Azure.|
| `Microsoft.Compute/virtualMachines/*` | Accorde l’accès à toutes les opérations des machines virtuelles et à leurs types de ressources enfants.|
| `microsoft.web/sites/restart/Action` | Accorde l’accès au redémarrage d’une application web.|

## <a name="notactions"></a>notActions

L’autorisation `notActions` spécifie les opérations de gestion qui sont exclues des `actions` autorisées. Utilisez l’autorisation `notActions` si l’ensemble des opérations que vous souhaitez autoriser est plus facile à définir en excluant les opérations restreintes. L’accès accordé par un rôle (autorisations effectives) est calculé en soustrayant les opérations `notActions` des opérations `actions`.

> [!NOTE]
> Si un utilisateur se voit attribuer un rôle qui exclut une opération dans `notActions`, et un second rôle qui accorde l’accès à cette même opération, il est autorisé à effectuer celle-ci. `notActions` n’est pas une règle de refus : il s’agit simplement d’un moyen pratique pour créer un ensemble d’opérations autorisées lorsque des opérations spécifiques doivent être exclues.
>

## <a name="assignablescopes"></a>assignableScopes

La section `assignableScopes` spécifie les étendues (groupes de gestion -actuellement en préversion-, abonnements, groupes de ressources ou ressources) dans lesquelles le rôle est disponible pour attribution. Vous pouvez rendre le rôle disponible pour attribution uniquement dans les abonnements ou les groupes de ressources qui le nécessitent, mais pas surcharger l’expérience utilisateur pour le reste des abonnements ou groupes de ressources. Vous devez utiliser au moins un groupe de gestion, abonnement, groupe de ressources ou ID de ressource.

La chaîne `assignableScopes` est définie sur l’étendue racine (`"/"`) pour les rôles intégrés. L’étendue racine indique que le rôle est disponible pour attribution dans toutes les étendues. Vous ne pouvez pas utiliser l’étendue racine dans vos propres rôles personnalisés. Si vous essayez, vous obtiendrez une erreur d’autorisation.

Voici des exemples d’étendues assignables valides :

| Scénario | Exemples |
|----------|---------|
| Rôle disponible pour attribution dans un seul abonnement | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rôle disponible pour attribution dans deux abonnements | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rôle disponible pour attribution uniquement dans le groupe de ressources réseau | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rôle disponible pour attribution dans toutes les étendues | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes et rôles personnalisés

La section `assignableScopes` pour un rôle personnalisé contrôle également qui peut créer, supprimer, modifier ou afficher le rôle personnalisé.

| Tâche | Opération | Description |
| --- | --- | --- |
| Créer/supprimer un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `assignableScopes` du rôle personnalisé peuvent créer (ou supprimer) des rôles personnalisés utilisables dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) d’abonnements, de groupes de ressources et de ressources. |
| Modifier un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/write` | Les utilisateurs ayant accès à cette opération sur toutes les étendues `assignableScopes` du rôle personnalisé peuvent modifier des rôles personnalisés dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) d’abonnements, de groupes de ressources et de ressources. |
| Afficher un rôle personnalisé | `Microsoft.Authorization/ roleDefinition/read` | Les utilisateurs ayant accès à cette opération dans une étendue peuvent afficher les rôles personnalisés disponibles pour attribution dans cette étendue. Tous les rôles intégrés permettent que les rôles personnalisés soient disponibles pour attribution. |

## <a name="role-definition-examples"></a>Exemples de définitions de rôles

L’exemple suivant présente la définition de rôle [Lecteur](built-in-roles.md#reader) tel qu’affichée à l’aide d’Azure CLI :

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Voir aussi

* [Rôles intégrés](built-in-roles.md)
* [Rôles personnalisés](custom-roles.md)
* [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md)
