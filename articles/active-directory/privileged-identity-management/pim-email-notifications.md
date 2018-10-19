---
title: Notifications par e-mail dans PIM - Azure | Microsoft Docs
description: Explique ce que sont les notifications par e-mail dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 09/07/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: de1d29d3ab1b370257c3a2d6b6ff9f677197fc2a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303062"
---
# <a name="email-notifications-in-pim"></a>Notifications par e-mail dans PIM

Lorsque des événements clés se produisent dans Azure AD Privileged Identity Management (PIM), des notifications par e-mail sont envoyées. Par exemple, PIM envoie des e-mails pour les événements suivants :

- Quand l’activation d’un rôle privilégié est en attente d’approbation
- Quand une demande d’activation de rôle privilégié est approuvée
- Quand un rôle privilégié est activé
- Quand un rôle privilégié est attribué
- Quand Azure AD PIM est activé

Des notifications par e-mail sont envoyées aux administrateurs suivants :

- Administrateur de rôle privilégié
- Security Administrator

Des notifications par e-mail sont également envoyées à l’utilisateur final auquel le rôle privilégié est assigné pour les événements suivants :

- Quand une demande d’activation de rôle privilégié est approuvée
- Quand un rôle privilégié est attribué

Depuis fin juillet 2018, les notifications par e-mail envoyées via PIM ont un nouvel expéditeur et une nouvelle conception visuelle. Cette mise à jour va impacter PIM pour Azure AD et PIM pour les ressources Azure. Tous les événements qui déclenchaient l’envoi d’une notification par e-mail dans la version précédente continueront d’envoyer des notifications. Le contenu de certains e-mails seront mises à jour, fournissant plus d’informations ciblées.

## <a name="sender-email-address"></a>Adresse e-mail de l’expéditeur

À compter de fin juillet 2018, les notifications par e-mail proviendront de l’adresse suivante :

- Adresse e-mail : **azure-noreply@microsoft.com**
- Nom d’affichage : Microsoft Azure

Dans l’ancienne version, les notifications par e-mail provenaient de l’adresse suivante :

- Adresse e-mail : **azureadnotifications@microsoft.com**
- Nom d’affichage : Service de notification de Microsoft Azure AD

## <a name="email-subject-line"></a>Ligne Objet d’e-mail

À partir de fin juillet 2018, les notifications par e-mail pour les rôles de ressources Azure et Azure AD comportent désormais un préfixe **PIM** dans la ligne d’objet. Voici un exemple :

- PIM : Le rôle Lecteur de sauvegarde a été définitivement affecté à Alain Charon.

## <a name="pim-emails-for-azure-ad-roles"></a>E-mails PIM pour les rôles Azure AD

À compter de fin juillet 2018, les notifications par e-mail PIM pour les rôles Azure AD auront une nouvelle conception visuelle. L’exemple suivant montre un exemple d’e-mail envoyé lorsqu’un utilisateur active un rôle privilégié pour l’entreprise fictive Contoso.

![Nouvelle adresse e-mail PIM pour les rôles Azure AD](./media/pim-email-notifications/email-directory-new.png)

Avant, lorsqu’un utilisateur activait un rôle privilégié, l’e-mail ressemblait à ceci :

![Ancienne adresse e-mail PIM pour les rôles Azure AD](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>E-mails PIM pour les rôles de ressources Azure

À compter de fin juillet 2018, les notifications par e-mail PIM pour les rôles de ressources Azure auront une nouvelle conception visuelle. L’exemple suivant montre un exemple d’e-mail envoyé lorsqu’un utilisateur se voit attribuer un rôle privilégié pour l’entreprise fictive Contoso.

![Nouvel e-mail PIM pour les rôles de ressources Azure](./media/pim-email-notifications/email-resources-new.png)

Auparavant, lorsqu’un utilisateur se voyait attribuer un rôle privilégié, l’e-mail ressemblait à ceci :

![Ancien e-mail PIM pour les rôles de ressources Azure](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’annuaire Azure AD dans PIM](pim-how-to-change-default-settings.md)
- [Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM](azure-ad-pim-approval-workflow.md)
