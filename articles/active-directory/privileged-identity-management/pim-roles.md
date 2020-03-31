---
title: Rôles que vous ne pouvez pas gérer dans Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Décrit les rôles que vous ne pouvez pas gérer dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895200"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rôles que vous ne pouvez pas gérer dans Privileged Identity Management

Azure Active Directory (Azure AD) Identity Management (PIM) vous permet de gérer tous les [rôles Azure AD](../users-groups-roles/directory-assign-admin-roles.md) et tous les [rôles de ressources Azure](../../role-based-access-control/built-in-roles.md). Ces rôles comprennent également les rôles personnalisés qui sont associés à vos groupes d’administration, abonnements, groupes de ressources et ressources. Toutefois, il y a quelques rôles que vous ne pouvez pas gérer dans PIM. Cet article décrit les rôles que vous ne pouvez pas gérer dans Privileged Identity Management (PIM).

## <a name="classic-subscription-administrator-roles"></a>Rôles d’administrateur d’abonnements classique

Vous ne pouvez pas gérer les rôles d’administrateur d’abonnements classiques suivants dans Privileged Identity Management :

- Administrateur de comptes
- Administrateur de services
- Coadministrateur

Pour plus d’informations sur les rôles d’administrateur d’abonnements classiques, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Rôles d’administrateur Office 365

Les rôles dans Exchange Online ou SharePoint Online, à l’exception des rôles d’administrateur Exchange et d’administrateur SharePoint, ne sont pas représentés dans Azure AD et ne peuvent donc pas être gérés dans Privileged Identity Management. Pour plus d’informations sur ces services Office 365, consultez [Rôles d’administrateur Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> L’administrateur SharePoint bénéficie d’un accès administratif à SharePoint Online par le biais du Centre d’administration SharePoint Online et peut effectuer presque toutes les tâches dans SharePoint Online. Les utilisateurs éligibles peuvent rencontrer des problèmes de latence en utilisant ce rôle dans SharePoint après son activation dans Privileged Identity Management.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)
