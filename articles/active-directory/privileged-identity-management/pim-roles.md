---
title: Rôles ne pouvant pas être gérés dans PIM - Azure Active Directory | Microsoft Docs
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
ms.date: 01/18/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d66d433d9de537358777e54e3c7d5489c25c849b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804090"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Rôles que vous ne pouvez pas gérer dans PIM

Azure Active Directory (Azure AD) Identity Management (PIM) vous permet de gérer tous les [rôles Azure AD](../users-groups-roles/directory-assign-admin-roles.md) et tous les [rôles de ressources Azure](../../role-based-access-control/built-in-roles.md). Ces rôles comprennent également les rôles personnalisés qui sont associés à vos groupes d’administration, abonnements, groupes de ressources et ressources. Toutefois, il y a quelques rôles que vous ne pouvez pas gérer dans PIM. Cet article décrit les rôles concernés.

## <a name="classic-subscription-administrator-roles"></a>Rôles d’administrateur d’abonnements classique

Vous ne pouvez pas gérer les rôles d’administrateur d’abonnements classiques suivants dans PIM :

- Administrateur de comptes
- Administrateur de services
- Coadministrateur

Pour plus d’informations sur les rôles d’administrateur d’abonnements classiques, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Rôles d’administrateur Office 365

Les rôles dans Exchange Online ou SharePoint Online, à l’exception des rôles d’administrateur Exchange et d’administrateur SharePoint, ne sont pas représentés dans Azure AD et ne peuvent donc pas être gérés dans PIM. Pour plus d’informations sur ces services Office 365, consultez [Rôles d’administrateur Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> L’administrateur SharePoint bénéficie d’un accès administratif à SharePoint Online par le biais du Centre d’administration SharePoint Online et peut effectuer presque toutes les tâches dans SharePoint Online. Les utilisateurs éligibles peuvent rencontrer des problèmes de latence en utilisant ce rôle dans SharePoint après son activation dans Privileged Identity Management (PIM).

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
