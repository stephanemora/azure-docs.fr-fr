---
title: Mettre à jour ou supprimer une attribution de rôle Azure AD personnalisée dans Privileged Identity Management (PIM) | Microsoft Docs
description: Mettre à jour ou supprimer une attribution de rôle Azure AD personnalisée dans Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace189db569941371026b76c4438515f4c53e77b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896510"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Mettre à jour ou supprimer un rôle personnalisé Azure AD attribué dans Privileged Identity Management

Cet article vous explique comment utiliser Privileged Identity Management (PIM) pour mettre à jour ou supprimer l’attribution juste-à-temps et limitée dans le temps aux rôles personnalisés créés pour la gestion des applications dans l’expérience d’administration Azure Active Directory (Azure AD). 

- Pour plus d’informations sur la création de rôles personnalisés pour déléguer la gestion des applications dans Azure AD, consultez la section [Personnaliser les rôles Administrateur dans Azure Active Directory (préversion)](../users-groups-roles/roles-custom-overview.md). 
- Si vous n’avez pas encore utilisé Privileged Identity Management, consultez la section [Commencer à utiliser Privileged Identity Management](pim-getting-started.md) pour en savoir plus.

> [!NOTE]
> Les rôles personnalisés Azure AD ne sont pas intégrés aux rôles d’annuaire intégrés au cours de la préversion. Une fois la fonctionnalité mise à la disposition générale, la gestion des rôles se déroule dans l’expérience des rôles intégrés.

## <a name="update-or-remove-an-assignment"></a>Mettre à jour ou supprimer une attribution

Suivez ces étapes pour mettre à jour ou supprimer une attribution de rôle personnalisée existante.

1. Connectez-vous à [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) dans le portail Azure avec un compte d’utilisateur affecté au rôle Administrateur de rôle privilégié.
1. Sélectionnez **Rôles personnalisés Azure AD (préversion)** .

    ![Sélectionner la préversion des rôles personnalisés Azure AD pour afficher les attributions de rôles éligibles](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Sélectionnez **Rôles** pour afficher la liste des **Attributions** de rôles personnalisés pour les applications Azure AD.

    ![Sélectionnez Rôles pour voir la liste des attributions de rôles éligibles](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Sélectionnez le rôle que vous souhaitez mettre à jour ou supprimer.
1. Recherchez l’attribution de rôle sous les onglets **Rôles éligibles** et **Rôles actifs**.
1. Sélectionnez **Mettre à jour** ou **Supprimer** pour mettre à jour ou supprimer l’attribution de rôle.

    ![Sélectionner Supprimer ou Mettre à jour dans l’attribution de rôle éligible](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Étapes suivantes

- [Activer un rôle personnalisé Azure AD](azure-ad-custom-roles-assign.md)
- [Attribuer un rôle personnalisé Azure AD](azure-ad-custom-roles-assign.md)
- [Configurer une attribution de rôle Azure AD personnalisée](azure-ad-custom-roles-configure.md)
