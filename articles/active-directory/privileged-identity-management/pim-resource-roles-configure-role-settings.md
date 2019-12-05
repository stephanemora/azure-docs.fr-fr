---
title: Configurer les paramètres des rôles de ressources Azure dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9acc1487fcbf8398b7a556c63f97963b264451
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74182710"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Configurer les paramètres des rôles de ressource Azure dans Privileged Identity Management

Lorsque vous configurez les paramètres des rôles de ressources Azure, vous définissez les paramètres par défaut appliqués aux attributions de rôles de ressources Azure dans Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Utilisez les procédures suivantes pour configurer le flux de travail d’approbation et spécifier qui peut approuver ou refuser les demandes.

## <a name="open-role-settings"></a>Ouvrir les paramètres des rôles

Suivez ces étapes pour ouvrir les paramètres pour un rôle de ressource Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un utilisateur avec le rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez **Ressources Azure**.

1. Sélectionnez la ressource que vous souhaitez gérer, telle qu’un abonnement ou un groupe d’administration.

    ![Page de ressources Azure qui répertorie les ressources pouvant être gérées](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Sélectionnez **Paramètres du rôle**.

    ![Page Paramètres de rôle répertoriant les rôles de ressources Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Sélectionnez le rôle dont vous souhaitez configurer les paramètres.

    ![Page Détails des paramètres de rôle répertoriant plusieurs paramètres d’affectation et d’activation](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Sélectionnez **Modifier** pour ouvrir le volet **Paramètres de rôle**. Le premier onglet vous permet de mettre à jour la configuration de l’activation de rôles dans Privileged Identity Management.

    ![Page Modifier les paramètres de rôle avec l’onglet Activation ouvert](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Sélectionnez l’onglet **Affectation** ou le bouton **Suivant : Affectation** au bas de la page pour ouvrir l’onglet du paramètre d’affectation. Ces paramètres gèrent les affectations de rôles effectuées dans l’interface Privileged Identity Management.

    ![Onglet Attribution de rôle dans la page des paramètres de rôle](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Utilisez l’onglet **Notification** ou le bouton **Suivant : Activation** au bas de la page pour accéder à l’onglet du paramètre de notification pour ce rôle. Ces paramètres gèrent tous les e-mails de notification relatifs à ce rôle.

    > [!NOTE]
    > L’interface utilisateur des notifications est actuellement déployée dans les régions et les abonnements Azure. Si vous ne pouvez pas configurer ces notifications précises, revenez vérifier dans un ou deux jours.

    ![Onglet Notifications de rôle dans la page des paramètres de rôle](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

1. Sélectionnez le bouton **Mettre à jour** quand vous voulez pour mettre à jour les paramètres de rôle.

## <a name="assignment-duration"></a>Durée de l’attribution

Vous pouvez choisir entre deux options de durée d’attribution pour chaque type d’attribution (éligible et actif) lorsque vous configurez les paramètres d’un rôle. Ces options deviennent la durée maximale par défaut lorsqu’un utilisateur est attribué au rôle dans Privileged Identity Management.

Vous pouvez choisir l’une de ces options de durée d’attribution **éligible** :

| | |
| --- | --- |
| **Autoriser une attribution éligible permanente** | Les administrateurs de ressources peuvent accorder une attribution éligible permanente. |
| **Faire expirer les attributions éligibles après** | Les administrateurs de ressources peuvent exiger que toutes les attributions éligibles aient une date de début et une date de fin spécifiées. |

Vous pouvez choisir l’une de ces options de durée d’attribution **active** :

| | |
| --- | --- |
| **Autoriser une attribution active permanente** | Les administrateurs de ressources peuvent accorder une attribution active permanente. |
| **Faire expirer les attributions actives après** | Les administrateurs de ressources peuvent exiger que toutes les attributions actives aient une date de début et une date de fin spécifiées. |

> [!NOTE]
> Toutes les attributions qui ont une date de fin spécifiée peuvent être renouvelées par les administrateurs de ressources. De plus, les utilisateurs peuvent lancer des demandes en libre-service afin d’[étendre ou renouveler des attributions de rôles](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exiger une authentification multifacteur

Privileged Identity Management permet également l’application facultative d’Azure Multi-Factor Authentication dans deux scénarios distincts.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Demander l'authentification multifacteur lors de l'attribution active

Dans certains cas, vous pouvez attribuer un utilisateur ou un groupe à un rôle pour une courte durée (une journée, par exemple). Les utilisateurs attribués n’ont alors pas besoin de demander l’activation. Dans ce scénario, Privileged Identity Management ne peut pas appliquer l’authentification multifacteur lorsque l’utilisateur utilise son attribution de rôle, car il est déjà actif dans le rôle depuis l’attribution.

Pour garantir que l’administrateur de ressources qui réalise l’attribution est bien celui qu’il prétend être, vous pouvez appliquer l’authentification multifacteur lors de l’attribution active en cochant la case **Demander l’authentification multifacteur lors de l’attribution active**.

### <a name="require-multi-factor-authentication-on-activation"></a>Exiger Multi-Factor Authentication lors de l’activation

Vous pouvez exiger des utilisateurs éligibles à un rôle qu’ils s’authentifient à l’aide d’Azure Multi-Factor Authentication pour effectuer l’activation. L’authentification multifacteur garantit, avec une certitude raisonnable, que l’utilisateur est bien celui qu’il prétend être. L’application de cette option permet de protéger les ressources critiques au cas où le compte d’utilisateur pourrait être compromis.

Pour exiger l’authentification multifacteur avant l’activation, cochez la case **Exiger l’authentification multifacteur lors de l’activation**.

Pour plus d’informations, consultez [Authentification multifacteur et Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durée maximum d'activation

Utilisez le curseur **Durée maximum d'activation** pour définir la durée maximale, en heures, pendant laquelle un rôle reste actif avant d’expirer. Cette valeur peut être comprise entre 1 et 24 heures.

## <a name="require-justification"></a>Demander une justification

Vous pouvez exiger que les utilisateurs saisissent une justification métier lorsqu’ils s’activent. Pour demander une justification, cochez la case **Demander une justification lors de l'affectation active** ou la case **Demander une justification lors de l’activation**.

## <a name="require-approval-to-activate"></a>Demander une approbation pour activation

Si vous souhaitez exiger une approbation pour activer un rôle, procédez comme suit.

1. Cochez la case **Exiger une approbation pour activer**.

1. Cliquez sur **Sélectionner des approbateurs** pour ouvrir la page **Sélectionner un membre ou un groupe**.

    ![Sélectionner un volet d’utilisateur ou de groupe pour sélectionner les approbateurs](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Sélectionnez au moins un utilisateur ou un groupe, puis cliquez sur **Sélectionner**. Vous pouvez ajouter n’importe quelle combinaison d’utilisateurs et de groupes. Vous devez sélectionner au moins un approbateur. Il n’existe aucun approbateur par défaut.

    Vos sélections figurent dans la liste des approbateurs sélectionnés.

1. Une fois que vous avez spécifié tous vos paramètres de rôle, sélectionnez **Mettre à jour** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Configurer les alertes de sécurité pour les rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-configure-alerts.md)
