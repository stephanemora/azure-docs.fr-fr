---
title: Configurer un rôle personnalisé Azure AD - Privileged Identity Management (PIM)
description: Configurer des rôles personnalisés Azure AD dans Privileged Identity Management (PIM)
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
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498681"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Configurer des rôles personnalisés Azure AD dans Privileged Identity Management

Un administrateur de rôle privilégié peut modifier les paramètres de rôle qui s’appliquent à un utilisateur lorsqu’il active son attribution à un rôle personnalisé et à d’autres administrateurs d’applications qui attribuent des rôles personnalisés.

> [!NOTE]
> Les rôles personnalisés Azure AD ne sont pas intégrés aux rôles d’annuaire intégrés au cours de la préversion. Une fois la fonctionnalité mise à la disposition générale, la gestion des rôles se déroule dans l’expérience des rôles intégrés. Si vous voyez la bannière suivante, ces rôles doivent être gérés [dans l’expérience des rôles intégrés](pim-how-to-activate-role.md) et cet article ne s’applique pas :
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Ouvrir les paramètres de rôle

Suivez ces étapes pour ouvrir les paramètres d’un rôle Azure AD.

1. Connectez-vous à [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) dans le portail Azure en utilisant un compte d’utilisateur qui est affecté au rôle Administrateur de rôle privilégié.
1. Sélectionnez **Rôles personnalisés Azure AD (préversion)** .

    ![Sélectionner la fonctionnalité Rôles personnalisés Azure AD (préversion) pour voir les attributions de rôles éligibles](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Sélectionnez **Paramètre** pour ouvrir la page **Paramètres**. Sélectionnez le rôle des paramètres que vous souhaitez configurer.
1. Sélectionnez **Modifier** pour ouvrir la page **Paramètres de rôle**.

    ![Ouvrir le rôle personnalisé Azure AD pour modifier les paramètres](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Paramètres de rôle

Vous pouvez configurer plusieurs paramètres.

### <a name="assignment-duration"></a>Durée de l’attribution

Vous pouvez choisir entre deux options de durée d’attribution pour chaque type d’attribution (éligible ou actif) lorsque vous configurez les paramètres d’un rôle. Ces options deviennent la durée maximale par défaut lorsqu’un membre est attribué au rôle dans Privileged Identity Management.

Vous pouvez choisir l’une de ces options de durée d’attribution *éligible*.

- **Autoriser une attribution éligible permanente** : les administrateurs peuvent attribuer une appartenance éligible permanente.
- **Faire expirer les attributions éligibles après** : les administrateurs peuvent exiger que toutes les attributions éligibles aient une date de début et une date de fin spécifiées.

Vous pouvez également choisir l’une de ces options de durée d’attribution *active* :

- **Autoriser une attribution active permanente** : les administrateurs peuvent attribuer une appartenance active permanente.
- **Faire expirer les attributions actives après** : les administrateurs peuvent exiger que toutes les attributions actives aient une date de début et une date de fin spécifiées.

### <a name="require-azure-multi-factor-authentication"></a>Exiger l’utilisation d’Azure Multi-Factor Authentication

Privileged Identity Management permet également l’implémentation facultative d’Azure Multi-Factor Authentication dans deux scénarios distincts.

- **Exiger Azure Multi-Factor Authentication lors de l’attribution active**

  Si vous souhaitez attribuer un rôle à un membre pour une courte durée uniquement (une journée par exemple), le processus est parfois trop lent pour exiger que les membres attribués ne demandent une activation. Dans ce scénario, Privileged Identity Management ne peut pas implémenter l’authentification multifacteur lorsque l’utilisateur active son attribution de rôle, car il est déjà actif dans le rôle depuis son attribution. Pour garantir que l’administrateur qui réalise l’attribution est bien celui qu’il prétend être, cochez la case **Exiger Azure Multi-Factor Authentication lors de l’attribution active**.

- **Exiger Azure Multi-Factor Authentication lors de l’attribution active**

  Vous pouvez exiger des utilisateurs éligibles à un rôle qu’ils s’inscrivent à Azure Multi-Factor Authentication pour effectuer leur activation. Ce processus garantit, avec une certitude raisonnable, que l’utilisateur demandant l’activation est bien celui qu’il prétend être. L’activation de cette option permet de protéger les rôles critiques au cas où le compte d’utilisateur serait compromis. Pour exiger d’un membre éligible qu’il exécute Azure Multi-Factor Authentication avant l’activation, cochez la case **Exiger Azure Multi-Factor Authentication lors de l’attribution active**.

Pour plus d’informations, consultez [Authentification multifacteur et Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Durée maximum d’activation

Utilisez le curseur **Durée maximum d’activation** pour définir la durée maximale, en heures, pendant laquelle un rôle reste actif avant d’expirer. Cette valeur peut être comprise entre 1 et 24 heures.

### <a name="require-justification"></a>Demander une justification

Vous pouvez exiger des membres qu’ils entrent une justification lors de l’attribution active ou quand ils effectuent l’activation. Pour demander une justification, cochez la case **Demander une justification lors de l’affectation active** ou la case **Demander une justification lors de l’activation**.

### <a name="require-approval-to-activate"></a>Demander une approbation pour activation

Si vous souhaitez exiger l’approbation pour activer un rôle, suivez ces étapes.

1. Cochez la case **Demander une approbation pour activation**.
1. Sélectionnez **Sélectionner des approbateurs** pour ouvrir la liste **Sélectionner un membre ou un groupe**.

    ![Ouvrir le rôle personnalisé Azure AD pour modifier les paramètres](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Sélectionnez au moins un membre ou un groupe, puis cliquez sur **Sélectionner**. Vous devez sélectionner au moins un approbateur. Il n’existe aucun approbateur par défaut. Vos sélections apparaissent dans la liste des approbateurs sélectionnés.
1. Une fois que vous avez spécifié les paramètres de rôle, sélectionnez **Mettre à jour** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

- [Activer un rôle personnalisé Azure AD](azure-ad-custom-roles-activate.md)
- [Attribuer un rôle personnalisé Azure AD](azure-ad-custom-roles-assign.md)
- [Supprimer ou mettre à jour une attribution de rôle personnalisé Azure AD](azure-ad-custom-roles-update-remove.md)
- [Définitions de rôles dans Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
