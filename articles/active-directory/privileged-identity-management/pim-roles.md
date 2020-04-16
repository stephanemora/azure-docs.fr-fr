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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607525"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rôles que vous ne pouvez pas gérer dans Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vous permet de gérer tous les [rôles Azure AD](../users-groups-roles/directory-assign-admin-roles.md) et tous les [rôles Azure](../../role-based-access-control/built-in-roles.md). Les rôles Azure peuvent également inclure vos rôles personnalisés, attachés à vos groupes d’administration, abonnements, groupes de ressources et ressources. Toutefois, il y a quelques rôles que vous ne pouvez pas gérer dans PIM. Cet article décrit les rôles que vous ne pouvez pas gérer dans Privileged Identity Management (PIM).

## <a name="classic-subscription-administrator-roles"></a>Rôles d’administrateur d’abonnements classique

Vous ne pouvez pas gérer les rôles d’administrateur d’abonnements classiques suivants dans Privileged Identity Management :

- Administrateur de comptes
- Administrateur de services
- Coadministrateur

Pour plus d’informations sur les rôles d’administrateur d’abonnements classiques, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Rôles d’administrateur Office 365

Nous prenons en charge tous les rôles Office 365 dans l’expérience de portail Rôles et administrateurs Azure AD, tels que l’administrateur Exchange et l’administrateur SharePoint, mais nous ne prenons pas en charge les rôles spécifiques dans les sessions RBAC Exchange ou RBAC SharePoint. Pour plus d’informations sur ces services Office 365, consultez [Rôles d’administrateur Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Les utilisateurs éligibles au rôle d’administrateur SharePoint, ainsi que tous les rôles tentant d’accéder au Centre de sécurité et de conformité Microsoft peuvent rencontrer des retards pouvant atteindre plusieurs heures après l’activation de leur rôle. Nous travaillons en collaboration avec ces équipes pour résoudre les problèmes.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)
