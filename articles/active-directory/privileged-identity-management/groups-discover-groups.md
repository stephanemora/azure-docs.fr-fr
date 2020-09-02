---
title: Gérer les groupes assignables à un rôle en tant que groupes d'accès privilégiés - Azure AD | Microsoft Docs
description: Consentez à intégrer des groupes assignables à un rôle pour les gérer en tant que groupes d'accès privilégiés dans Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869527"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Gérer des groupes d'accès privilégiés (préversion) dans Privileged Identity Management

Dans Azure Active Directory (Azure AD), vous pouvez attribuer des rôles intégrés Azure AD à des groupes cloud pour simplifier la gestion des attributions de rôles. Pour protéger les rôles Azure AD et sécuriser l’accès, vous pouvez désormais utiliser Privileged Identity Management (PIM) pour gérer l’accès juste-à-temps pour les membres ou les propriétaires de ces groupes. Pour gérer un groupe assignable à un rôle Azure AD en tant que groupe d’accès privilégié dans Privileged Identity Management, vous devez l’amener sous la gestion de PIM.

## <a name="identify-groups-to-manage"></a>Identifier les groupes à gérer

Vous pouvez créer un groupe assignable à un rôle dans Azure AD comme décrit dans [Créer un groupe assignable à un rôle dans Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). Vous devez être propriétaire du groupe pour assurer sa gestion dans Privileged Identity Management.

1. [Connectez-vous à Azure AD](https://aad.portal.azure.com) avec les autorisations d'Administrateur de rôle privilégié.
1. Sélectionnez **Groupes**, puis sélectionnez le groupe assignable à un rôle que vous souhaitez gérer. Vous pouvez effectuer une recherche dans la liste ou filtrer celle-ci.

    ![Rechercher un groupe assignable à un rôle à gérer dans PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Ouvrez le groupe et sélectionnez **Accès privilégié (préversion)** .

    ![Ouvrir l’expérience Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Si vos groupes ne sont pas encore gérés dans PIM, sélectionnez **Activer l'accès privilégié** pour consentir à la gestion. Seul l'Administrateur général ou le Propriétaire d'un groupe peut donner ce consentement.

    ![Consentez à gérer le groupe dans Privileged Identity Management si nécessaire](./media/groups-discover-groups/consent-page.png)

1. Commencez à gérer les affectations dans PIM.

    ![Gérer les affectations dans Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Une fois qu’un groupe d’accès privilégié est géré, il ne peut pas être supprimé de la gestion. Cela empêche tout autre administrateur de supprimer des paramètres de Privileged Identity Management (PIM).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les affectations de groupe d’accès privilégié dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Affecter des groupes d’accès privilégié dans Privileged Identity Management](pim-resource-roles-assign-roles.md)
