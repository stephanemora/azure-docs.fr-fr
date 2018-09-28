---
title: Comprendre les attributions de refus dans le RBAC Azure | Microsoft Docs
description: En savoir plus sur les attributions de refus dans le contrôle d’accès en fonction du rôle (RBAC) pour les ressources dans Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980166"
---
# <a name="understand-deny-assignments"></a>Comprendre les attributions de refus

De façon similaire à une attribution de rôle, une *attribution de refus* lie un ensemble d’actions de refus à un utilisateur, un groupe ou un principal de service dans une étendue spécifique, dans le but de refuser l’accès. Une attribution de refus peut également exclure des principaux et empêcher l’héritage dans les étendues enfants, qui est différent des attributions de rôles. Actuellement, les attributions de refus sont **en lecture seule** et peuvent être définies uniquement par Azure. Cet article explique comment définir des attributions de refus.

## <a name="deny-assignment-properties"></a>Propriétés des attributions de refus

 Une attribution de refus possède les propriétés suivantes :

> [!div class="mx-tableFixed"]
> | Propriété | Obligatoire | type | Description |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Oui | Chaîne | Nom d'affichage de l’attribution de refus. Les noms doivent être uniques pour une étendue donnée. |
> | `Description` | Non  | Chaîne | Description de l’attribution de refus. |
> | `Permissions.Actions` | Au moins un élément Actions ou DataActions | String[] | Tableau de chaînes qui spécifient les opérations de gestion auxquelles l’attribution de refus bloque l’accès. |
> | `Permissions.NotActions` | Non  | String[] | Tableau de chaînes qui spécifient les opérations de gestion à exclure de l’attribution de refus. |
> | `Permissions.DataActions` | Au moins un élément Actions ou DataActions | String[] | Tableau de chaînes qui spécifient les opérations de données auxquelles l’attribution de refus bloque l’accès. |
> | `Permissions.NotDataActions` | Non  | String[] | Tableau de chaînes qui spécifient les opérations de données à exclure de l’attribution de refus. |
> | `Scope` | Non  | Chaîne | Chaîne qui spécifie l’étendue à laquelle l’attribution de refus s’applique. |
> | `DoNotApplyToChildScopes` | Non  | Booléen | Spécifie si l’attribution de refus s’applique aux étendues enfants. La valeur par défaut est false. |
> | `Principals[i].Id` | Oui | String[] | Tableau d’ID d’objets principaux Azure AD (utilisateur, groupe ou principal de service) auxquels s’applique l’attribution de refus. Définissez sur un GUID vide `00000000-0000-0000-0000-000000000000` pour représenter tout le monde. |
> | `Principals[i].Type` | Non  | String[] | Tableau de types d’objet représentés par Principals[i].Id. Affectez la valeur `Everyone` pour représenter tout le monde. |
> | `ExcludePrincipals[i].Id` | Non  | String[] | Tableau d’ID d’objets principaux Azure AD (utilisateur, groupe ou principal de service) auxquels l’attribution de refus ne s’applique pas. |
> | `ExcludePrincipals[i].Type` | Non  | String[] | Tableau de types d’objet représentés par ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Non  | Booléen | Spécifie si cette attribution de refus a été créée par Azure et ne peut pas être modifiée ou supprimée. Actuellement, toutes les attributions de refus sont protégées par le système. |

## <a name="everyone-principal"></a>Principal Tout le monde

Pour prendre en charge les attributions de refus, le principal Tout le monde a été introduit. Le principal Tout le monde représente tous les utilisateurs, groupes et principaux de service figurant dans un annuaire Azure AD. Si l’ID de principal est un GUID nul `00000000-0000-0000-0000-000000000000` et le type de principal `Everyone`, le principal représente tout le monde. Le principal Tout le monde peut être associé à `ExcludePrincipals` pour refuser tout le monde, à l’exception de certains utilisateurs. Le principal Tout le monde a les contraintes suivantes :

- Il peut être utilisé uniquement dans `Principals` et ne peut pas être utilisé dans `ExcludePrincipals`.
- `Principals[i].Type` doit être défini sur `Everyone`.

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les attributions de refus à l’aide de RBAC et de l’API REST](deny-assignments-rest.md)
* [Comprendre les définitions de rôles](role-definitions.md)
