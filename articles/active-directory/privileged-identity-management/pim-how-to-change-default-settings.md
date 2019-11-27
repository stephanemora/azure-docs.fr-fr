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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021811"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurer les paramètres des rôles Azure AD dans Privileged Identity Management

Un administrateur de rôle privilégié peut personnaliser Privileged Identity Management (PIM) dans son organisation Azure Active Directory (Azure AD), notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle éligible.

## <a name="open-role-settings"></a>Ouvrir les paramètres des rôles

Suivez ces étapes pour ouvrir les paramètres pour un rôle Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Settings**.

    ![Rôles Azure AD - Paramètres](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Cliquez sur **Rôles**.

1. Cliquez sur le rôle dont vous souhaitez configurer les paramètres.

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

1. Pour spécifier des approbateurs, cliquez sur **Sélectionner des approbateurs**.

    ![Rôles Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Sélectionnez un ou plusieurs approbateurs en plus de l’administrateur de rôle privilégié, puis cliquez sur **Sélectionner**. Vous pouvez sélectionner des utilisateurs ou des groupes. Nous vous recommandons d’utiliser au moins deux approbateurs. Même si vous vous ajoutez vous-même en tant qu’approbateur, vous ne pouvez pas approuver l’activation d’un rôle pour vous-même. Vos sélections figurent dans la liste des approbateurs sélectionnés.

1. Après avoir spécifié tous vos paramètres de rôle, sélectionnez **Enregistrer** pour enregistrer vos modifications.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurer les alertes de sécurité pour les rôles Azure AD dans Privileged Identity Management](pim-how-to-configure-security-alerts.md)
