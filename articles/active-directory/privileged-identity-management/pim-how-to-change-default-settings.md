---
title: Configurer les paramètres des rôles Azure AD dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e15418524790e2169a6f60b79629f15f1d141ec4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429641"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurer les paramètres des rôles Azure AD dans Privileged Identity Management

Un administrateur de rôle privilégié peut personnaliser Privileged Identity Management (PIM) dans son organisation Azure Active Directory (Azure AD), notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle éligible.

## <a name="determine-your-version-of-pim"></a>Déterminer votre version de PIM

Depuis novembre 2019, la partie Rôles Azure AD de Privileged Identity Management est mise à jour vers une nouvelle version qui correspond à l’expérience des rôles de ressources Azure. Cela permet de créer des fonctionnalités supplémentaires et d’apporter des [modifications à l’API existante](azure-ad-roles-features.md#api-changes). Pendant que la nouvelle version est déployée, les procédures que vous suivez dans cet article dépendent de la version de Privileged Identity Management que vous possédez actuellement. Pour déterminer la version de Privileged Identity Management dont vous disposez, procédez de la manière décrite dans cette section. Une fois que vous connaissez votre version de Privileged Identity Management, vous pouvez sélectionner les procédures de cet article qui correspondent à cette version.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un utilisateur avec le rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).
1. Ouvrez **Azure AD Privileged Identity Management**. Si une bannière figure en haut de la page de présentation, suivez les instructions sous l’onglet **Nouvelle version** de cet article. Sinon, suivez les instructions sous l’onglet **Version précédente**.

    ![Nouvelle version des rôles Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

Suivez les étapes décrites dans cet article pour approuver ou refuser des demandes de rôles requête HTTP.

# <a name="previous-versiontabprevious"></a>[Version précédente](#tab/previous)

## <a name="open-role-settings"></a>Ouvrir les paramètres des rôles

Suivez ces étapes pour ouvrir les paramètres pour un rôle Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Paramètres**.

    ![Rôles Azure AD - Paramètres](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Sélectionnez **Rôles**.

1. Sélectionnez le rôle dont vous souhaitez configurer les paramètres.

    ![Rôles Azure AD - Paramètres des rôles](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Dans la page des paramètres de chaque rôle, vous pouvez configurer plusieurs paramètres. Ces paramètres affectent uniquement les utilisateurs qui sont **éligibles**, et non ceux qui sont **permanents**.

## <a name="activations"></a>Activations

Utilisez le curseur **Activations** pour définir la durée maximale (en heures) pendant laquelle un rôle reste actif avant d’expirer. Cette valeur peut être comprise entre 1 et 72 heures.

## <a name="notifications"></a>Notifications

Utilisez le commutateur **Notifications** pour spécifier si les administrateurs reçoivent une notification quand les rôles sont activés. Cette notification peut être utile pour détecter les activations non autorisées ou illégitimes.

Quand la valeur est **Activer**, les notifications sont envoyées aux personnes suivantes :

- Administrateur de rôle privilégié
- Administrateur de sécurité
- Administrateur général

Pour plus d’informations, consultez [Notification par e-mail dans Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Ticket d’incident/de demande

Utilisez le commutateur **Ticket d’incident/de demande** pour exiger que les administrateurs éligibles incluent un numéro de ticket lorsqu’ils activent leur rôle. Cette pratique peut rendre les audits d’accès en fonction du rôle plus efficaces.

## <a name="multi-factor-authentication"></a>Azure Multi-Factor Authentication

Utilisez le commutateur **Authentification multifacteur** pour spécifier si les utilisateurs doivent ou non confirmer leur identité via l’authentification multifacteur avant de pouvoir activer leurs rôles. Une seule vérification de leur identité est nécessaire par session, et non chaque fois qu’ils ont activé un rôle. Il existe deux conseils à garder à l’esprit lorsque vous activez l’authentification multifacteur :

- Les utilisateurs qui disposent de comptes Microsoft pour leurs adresses e-mail (généralement @outlook.com, mais pas toujours) ne peuvent pas s’inscrire à Azure Multi-Factor Authentication. Si vous souhaitez attribuer des rôles aux utilisateurs disposant de comptes Microsoft, vous devez les rendre administrateurs permanents ou désactiver l’authentification multifacteur pour ce rôle.
- Vous ne pouvez pas désactiver Azure Multi-Factor Authentication pour les rôles à privilèges élevés pour Azure AD et Office 365. Cette fonctionnalité de sécurité permet de protéger les rôles suivants :  
  
  - Administrateur Azure Information Protection
  - Administrateur de facturation
  - Administrateur d’application cloud
  - Administrateur de conformité
  - Administrateur de l’accès conditionnel
  - Administrateur Dynamics 365
  - Approbateur d’accès au référentiel sécurisé client
  - Enregistreurs de répertoire
  - Administrateur Exchange
  - Administrateur général
  - Administrateur Intune
  - Administrateur Power BI
  - Administrateur de rôle privilégié
  - Administrateur de sécurité
  - Administrateur SharePoint
  - Administrateur Skype Entreprise
  - Administrateur d’utilisateurs

Pour plus d’informations, consultez [Authentification multifacteur et Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exiger une approbation

Si vous souhaitez déléguer l’approbation exigée pour activer un rôle, suivez ces étapes.

1. Réglez le commutateur **Exiger l'approbation** sur **Activé**. Le volet affiche les options permettant de sélectionner des approbateurs.

    ![Rôles Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Si vous ne spécifiez aucun approbateur, l’administrateur de rôle privilégié devient l’approbateur par défaut, et c’est alors lui qui doit approuver toutes les demandes d’activation pour ce rôle.

1. Pour ajouter des approbateurs, cliquez sur **Sélectionner des approbateurs**.

    ![Rôles Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Sélectionnez un ou plusieurs approbateurs en plus de l’administrateur de rôle privilégié, puis cliquez sur **Sélectionner**. Vous pouvez sélectionner des utilisateurs ou des groupes. Nous vous recommandons d’ajouter au moins deux approbateurs. Même si vous vous ajoutez vous-même en tant qu’approbateur, vous ne pouvez pas approuver l’activation d’un rôle pour vous-même. Vos sélections figurent dans la liste des approbateurs sélectionnés.

1. Après avoir spécifié tous vos paramètres de rôle, sélectionnez **Enregistrer** pour enregistrer vos modifications.

# <a name="new-versiontabnew"></a>[Nouvelle version](#tab/new)

## <a name="open-role-settings"></a>Ouvrir les paramètres des rôles

Suivez ces étapes pour ouvrir les paramètres pour un rôle Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un utilisateur avec le rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez **Paramètres du rôle**.

    ![Page Paramètres de rôle répertoriant les rôles de ressources Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Sélectionnez le rôle dont vous souhaitez configurer les paramètres.

    ![Page Détails des paramètres de rôle répertoriant plusieurs paramètres d’affectation et d’activation](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Sélectionnez **Modifier** pour ouvrir la page Paramètres de rôle.

    ![Modifier la page des paramètres de rôle avec des options permettant de mettre à jour les paramètres d’affectation et d’activation](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Dans le volet Paramètres de rôle de chaque rôle, vous pouvez configurer plusieurs paramètres.

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

---

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurer les alertes de sécurité pour les rôles Azure AD dans Privileged Identity Management](pim-how-to-configure-security-alerts.md)
