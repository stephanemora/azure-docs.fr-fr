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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: fa1a979c01999bd79c45d24e4c7771edaf346dd8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632413"
---
# <a name="understand-deny-assignments"></a>Comprendre les attributions de refus

À l’instar d’une attribution de rôle, une *affectation de refus* associe un ensemble d’actions de refus à un utilisateur, un groupe ou un principal de service, sur une étendue spécifique, afin de refuser l’accès. Les affectations de refus empêchent les utilisateurs d’effectuer des actions particulières, même si une attribution de rôle leur accorde l’accès. Dans Azure, certains fournisseurs de ressources incluent désormais des affectations de refus. Actuellement, les affectations de refus sont **en lecture seule** et peuvent être définies uniquement par Azure.

À certains égards, les affectations de refus sont différentes des attributions de rôles. Les affectations de refus peuvent exclure des principaux et empêcher la transmission à des étendues enfant. Les affectations de refus s’appliquent également aux affectations d’[administrateurs d’abonnements classiques](rbac-and-directory-admin-roles.md).

Cet article explique comment définir des attributions de refus.

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
> | `Principals[i].Id` | Oui | String[] | Tableau d’ID d’objets principaux Azure AD (utilisateur, groupe, principal de service ou identité managée) auxquels s’applique l’affectation de refus. Définie sur un GUID vide `00000000-0000-0000-0000-000000000000` pour représenter tous les principaux. |
> | `Principals[i].Type` | Non  | String[] | Tableau de types d’objet représentés par Principals[i].Id. Définie sur `SystemDefined` pour représenter tous les principaux. |
> | `ExcludePrincipals[i].Id` | Non  | String[] | Tableau d’ID d’objets principaux Azure AD (utilisateur, groupe, principal de service ou identité managée) auxquels l’attribution de refus ne s’applique pas. |
> | `ExcludePrincipals[i].Type` | Non  | String[] | Tableau de types d’objet représentés par ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Non  | Booléen | Spécifie si cette attribution de refus a été créée par Azure et ne peut pas être modifiée ou supprimée. Actuellement, toutes les attributions de refus sont protégées par le système. |

## <a name="system-defined-principal"></a>Principal défini par le système

Le **principal défini par le système** a été introduit pour prendre en charge les affectations de refus. Ce principal représente tous les utilisateurs, groupes, principaux de service et identités managées figurant dans un annuaire Azure AD. Si l’ID du principal est un GUID nul `00000000-0000-0000-0000-000000000000`, et le type de principal `SystemDefined`, le principal représente tous les principaux. `SystemDefined` peut être combiné avec `ExcludePrincipals` pour refuser tous les principaux, à l’exception de certains utilisateurs. `SystemDefined` présente les contraintes suivantes :

- Il peut être utilisé uniquement dans `Principals` et ne peut pas être utilisé dans `ExcludePrincipals`.
- `Principals[i].Type` doit être défini sur `SystemDefined`.

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les attributions de refus à l’aide de RBAC et de l’API REST](deny-assignments-rest.md)
* [Comprendre les définitions de rôles](role-definitions.md)
