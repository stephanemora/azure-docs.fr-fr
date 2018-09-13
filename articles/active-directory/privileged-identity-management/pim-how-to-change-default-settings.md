---
title: Configurer les paramètres des rôles d’annuaire Azure AD dans PIM | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles d’annuaire Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2d7226f18eb922eaba3c8184656560c33202ef56
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665432"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>Configurer les paramètres des rôles d’annuaire Azure AD dans PIM

Un administrateur de rôle privilégié peut personnaliser Azure AD Privileged Identity Management (PIM) dans son organisation, notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle éligible.

## <a name="open-role-settings"></a>Ouvrir les paramètres des rôles

Suivez ces étapes pour ouvrir les paramètres pour un rôle d’annuaire Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles d’annuaire Azure AD**.

1. Cliquez sur **Settings**.

    ![Rôles d’annuaire Azure AD - Paramètres](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Cliquez sur **Rôles**.

1. Cliquez sur le rôle dont vous souhaitez configurer les paramètres.

    ![Rôles d’annuaire Azure AD - Paramètres des rôles](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Dans la page des paramètres de chaque rôle, vous pouvez configurer plusieurs paramètres. Ces paramètres affectent uniquement les utilisateurs qui sont **éligibles**, et non ceux qui sont **permanents**.

## <a name="activations"></a>Activations

Utilisez le curseur **Activations** pour définir la durée maximale (en heures) pendant laquelle un rôle reste actif avant d’expirer. Cette valeur peut être comprise entre 1 et 72 heures.

## <a name="notifications"></a>Notifications

Utilisez le commutateur **Notifications** pour spécifier si le système doit envoyer ou non des e-mails aux administrateurs pour leur confirmer qu’ils ont activé un rôle. Cette option peut être utile pour détecter les activations non autorisées ou illégitimes.

## <a name="incidentrequest-ticket"></a>Ticket d’incident/de demande

Utilisez le commutateur **Ticket d’incident/de demande** pour spécifier si les administrateurs éligibles doivent ou non inclure un numéro de ticket lorsqu’ils activent leur rôle. Cela peut être utile lorsque vous effectuez des audits d’accès à un rôle.

## <a name="multi-factor-authentication"></a>Azure Multi-Factor Authentication

Utilisez le commutateur **Authentification multifacteur** pour spécifier si les utilisateurs doivent ou non confirmer leur identité via l’authentification multifacteur avant de pouvoir activer leurs rôles. Une seule vérification est nécessaire par session, et non chaque fois qu’ils ont activé un rôle. Il existe deux conseils à garder à l’esprit lorsque vous activez l’authentification multifacteur :

* Les utilisateurs qui disposent de comptes Microsoft pour leurs adresses de messagerie (généralement @outlook.com mais pas toujours) ne peuvent pas s’inscrire à Azure MFA. Si vous souhaitez attribuer des rôles aux utilisateurs disposant de comptes Microsoft, vous devez les rendre administrateurs permanents ou désactiver l’authentification multifacteur pour ce rôle.
* Vous ne pouvez pas désactiver l’authentification multifacteur pour les rôles à privilèges élevés pour Azure AD et Office 365. Il s’agit d’une fonctionnalité de sécurité car ces rôles doivent être soigneusement protégés :  
  
  * Administrateur d’application
  * Administrateur du serveur proxy d’application
  * Administrateur de facturation  
  * Administrateur de conformité  
  * Administrateur de services CRM
  * Approbateur d’accès au référentiel sécurisé client
  * Enregistreur de répertoire  
  * Administrateur Exchange  
  * Administrateur général
  * Administrateur de services Intune
  * Administrateur de boîte aux lettres  
  * Prise en charge de niveau 1 de partenaire  
  * Prise en charge de niveau 2 de partenaire  
  * Administrateur de rôle privilégié
  * Administrateur de sécurité  
  * Administrateur SharePoint  
  * Administrateur Skype Entreprise  
  * Administrateur de compte d’utilisateur  

Pour plus d’informations, consultez [Authentification multifacteur (MFA) et PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exiger une approbation

Si vous souhaitez exiger une approbation pour activer un rôle, procédez comme suit.

1. Réglez le commutateur **Exiger l'approbation** sur **Activé**. Le volet affiche les options permettant de sélectionner des approbateurs.

    ![Rôles d’annuaire Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Si vous **ne spécifiez pas** d’approbateurs, les administrateurs de rôle privilégié deviennent les approbateurs par défaut. Les administrateurs de rôle privilégié doivent approuver **TOUTES** les demandes d’activation pour ce rôle.

1. Pour spécifier des approbateurs, cliquez sur **Sélectionner des approbateurs**.

    ![Rôles d’annuaire Azure AD - Paramètres - Exiger une approbation](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Sélectionnez un ou plusieurs approbateurs, puis cliquez sur **Sélectionner**. Vous pouvez sélectionner des utilisateurs ou des groupes. Il est recommandé de sélectionner au moins deux approbateurs. L’auto-approbation n’est pas autorisée.

    Vos sélections figurent dans la liste des approbateurs sélectionnés.

1. Une fois que vous avez spécifié tous vos paramètres de rôle, cliquez sur **Enregistrer** pour enregistrer vos modifications.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles d’annuaire Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans PIM](pim-how-to-configure-security-alerts.md)
