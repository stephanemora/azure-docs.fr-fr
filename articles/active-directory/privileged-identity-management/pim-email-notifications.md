---
title: Notifications par e-mail dans Azure AD Privileged Identity Management | Microsoft Docs
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
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 7943b4fb8c2027b50ce04c30d21f1b0a58f98ace
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621580"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Notifications par e-mail dans Azure AD Privileged Identity Management

Lorsque des événements clés se produisent dans Azure AD Privileged Identity Management (PIM), des notifications par e-mail sont envoyées à l’administrateur ou à l’utilisateur concerné. Par exemple, PIM envoie des e-mails pour les événements suivants :

- Quand l’activation d’un rôle privilégié est en attente d’approbation
- Quand une demande d’activation d’un rôle privilégié est approuvée
- Quand un rôle privilégié est activé
- Quand un rôle privilégié est attribué
- Quand Azure AD PIM est activé

À compter de fin juillet 2018, les notifications par e-mail envoyées via PIM auront un nouvel expéditeur et une nouvelle conception visuelle. Cette mise à jour va impacter PIM pour Azure AD et PIM pour les ressources Azure. Tous les événements qui déclenchaient l’envoi d’une notification par e-mail dans la version précédente continueront d’envoyer des notifications. Le contenu de certains e-mails seront mises à jour, fournissant plus d’informations ciblées.

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

- [Comment gérer les paramètres d’activation de rôle dans Azure AD PIM](pim-how-to-change-default-settings.md)
- [Approbations dans Azure AD PIM](azure-ad-pim-approval-workflow.md)
