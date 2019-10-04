---
title: Configurer les paramètres des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804442"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Configurer les paramètres des rôles Azure AD dans PIM

Un administrateur de rôle privilégié peut personnaliser Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dans son organisation, notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle éligible.

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

Utilisez le commutateur **Notifications** pour spécifier si les administrateurs reçoivent une notification quand les rôles sont activés. Cette option peut être utile pour détecter les activations non autorisées ou illégitimes.

Quand la valeur est **Activer**, les notifications sont envoyées aux personnes suivantes :

- Administrateur de rôle privilégié
- Security Administrator
- Administrateur général

Pour plus d’informations, consultez [Notifications par e-mail dans PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Ticket d’incident/de demande

Utilisez le commutateur **Ticket d’incident/de demande** pour spécifier si les administrateurs éligibles doivent ou non inclure un numéro de ticket lorsqu’ils activent leur rôle. Cela peut être utile lorsque vous effectuez des audits d’accès à un rôle.

## <a name="multi-factor-authentication"></a>Azure Multi-Factor Authentication

Utilisez le commutateur **Authentification multifacteur** pour spécifier si les utilisateurs doivent ou non confirmer leur identité via l’authentification multifacteur avant de pouvoir activer leurs rôles. Une seule vérification est nécessaire par session, et non chaque fois qu’ils ont activé un rôle. Il existe deux conseils à garder à l’esprit lorsque vous activez l’authentification multifacteur :

* Les utilisateurs qui disposent de comptes Microsoft pour leurs adresses de messagerie (généralement @outlook.com mais pas toujours) ne peuvent pas s’inscrire à Azure MFA. Si vous souhaitez attribuer des rôles aux utilisateurs disposant de comptes Microsoft, vous devez les rendre administrateurs permanents ou désactiver l’authentification multifacteur pour ce rôle.
* Vous ne pouvez pas désactiver l’authentification multifacteur pour les rôles à privilèges élevés pour Azure AD et Office 365. Il s’agit d’une fonctionnalité de sécurité car ces rôles doivent être soigneusement protégés :  
  
  * Administrateur Azure Information Protection
  * Administrateur de facturation
  * Administrateur d'applications cloud
  * Administrateur de conformité
  * Administrateur de l’accès conditionnel
  * Administrateur de services CRM
  * Approbateur d’accès à Customer LockBox
  * Enregistreurs de répertoire
  * Administrateur Exchange
  * Administrateur général
  * Administrateur de services Intune
  * Administrateur du service Power BI
  * Administrateur de rôle privilégié
  * Security Administrator
  * Administrateur de services SharePoint
  * Administrateur Skype Entreprise
  * Administrateur d'utilisateurs

Pour plus d’informations, consultez [Authentification multifacteur (MFA) et PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exiger une approbation

Si vous souhaitez exiger une approbation pour activer un rôle, procédez comme suit.

1. Réglez le commutateur **Exiger l'approbation** sur **Activé**. Le volet affiche les options permettant de sélectionner des approbateurs.

    ![Rôles Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Si vous **ne spécifiez pas** d’approbateurs, les administrateurs de rôle privilégié deviennent les approbateurs par défaut. Les administrateurs de rôle privilégié doivent approuver **TOUTES** les demandes d’activation pour ce rôle.

1. Pour spécifier des approbateurs, cliquez sur **Sélectionner des approbateurs**.

    ![Rôles Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Sélectionnez un ou plusieurs approbateurs, puis cliquez sur **Sélectionner**. Vous pouvez sélectionner des utilisateurs ou des groupes. Il est recommandé de sélectionner au moins deux approbateurs. L’auto-approbation n’est pas autorisée.

    Vos sélections figurent dans la liste des approbateurs sélectionnés.

1. Une fois que vous avez spécifié tous vos paramètres de rôle, cliquez sur **Enregistrer** pour enregistrer vos modifications.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Configurer des alertes de sécurité pour les rôles Azure AD dans PIM](pim-how-to-configure-security-alerts.md)
