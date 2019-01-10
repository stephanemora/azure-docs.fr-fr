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
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 4bc963d09b9d01efdfad65b78ce9ce336798d375
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065339"
---
# <a name="email-notifications-in-pim"></a>Notifications par e-mail dans PIM

Azure AD Privileged Identity Management (PIM) vous informe quand des événements importants se produisent, par exemple l’attribution ou l’activation d’un rôle. Pour vous tenir informé, PIM envoie des notifications par e-mail qui vous sont adressées à vous et à d’autres participants. Ces e-mails peuvent également inclure des liens vers des tâches appropriées, comme l’activation ou le renouvellement d’un rôle. Cet article décrit à quoi ressemblent ces e-mails, quand ils sont envoyés et qui les reçoit.

## <a name="sender-email-address-and-subject-line"></a>Adresse e-mail de l’expéditeur et ligne Objet

L’adresse de l’expéditeur des e-mails envoyés par PIM pour les rôles de ressources Azure AD et Azure est la suivante :

- Adresse e-mail : **azure-noreply@microsoft.com**
- Nom d’affichage : Microsoft Azure

Ces e-mails incluent un préfixe **PIM** dans la ligne Objet. Voici un exemple :

- PIM : Le rôle Lecteur de sauvegarde a été définitivement attribué à Alain Charon

## <a name="pim-emails-for-azure-ad-roles"></a>E-mails PIM pour les rôles Azure AD

PIM envoie des e-mails quand les événements suivants se produisent pour des rôles Azure AD :

- Quand l’activation d’un rôle privilégié est en attente d’approbation
- Quand une demande d’activation de rôle privilégié est approuvée
- Quand Azure AD PIM est activé

Les destinataires de ces e-mails pour les rôles Azure AD varient selon votre rôle, l’événement et le paramètre de notification :

| Utilisateur | Une activation d’un rôle est en attente d’approbation | Une demande d’activation de rôle est terminée | PIM est activé |
| --- | --- | --- | --- |
| Administrateur de rôle privilégié</br>(Activé/éligible) | Oui</br>(uniquement si aucun approbateur explicite n’est spécifié) | Oui* | Oui |
| Security Administrator</br>(Activé/éligible) | Non  | Oui* | Oui |
| Administrateur général</br>(Activé/éligible) | Non  | Oui* | Oui |

\* Si le paramètre [**Notifications**](pim-how-to-change-default-settings.md#notifications) a la valeur **Activer**.

L’exemple suivant montre un exemple d’e-mail envoyé quand un utilisateur active un rôle Azure AD pour l’organisation fictive Contoso.

![Nouvelle adresse e-mail PIM pour les rôles Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>E-mail PIM de synthèse hebdomadaire pour les rôles Azure AD

Un e-mail PIM récapitulatif pour les rôles Azure AD est envoyé chaque semaine aux administrateurs de rôles privilégiés, administrateurs de sécurité et administrateurs généraux qui ont activé PIM. Cet e-mail hebdomadaire fournit un instantané des activités PIM pour la semaine, ainsi que les attributions de rôles privilégiés. Il est uniquement disponible pour les locataires sur le cloud public. Voici un exemple d’e-mail :

![E-mail PIM de synthèse hebdomadaire pour les rôles Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Cet e-mail comprend quatre vignettes :

| Vignette | Description |
| --- | --- |
| **Utilisateurs activés** | Nombre de fois que les utilisateurs ont activé leur rôle éligible à l’intérieur du locataire. |
| **Utilisateurs devenus permanents** | Nombre de fois que les utilisateurs avec une attribution éligible sont devenus permanents. |
| **Attributions de rôles dans PIM** | Nombre de fois que les utilisateurs se voient attribuer un rôle éligible dans PIM. |
| **Attributions de rôles en dehors de PIM** | Nombre de fois que les utilisateurs se voient attribuer un rôle permanent en dehors de PIM (dans Azure AD). |

La **vue d’ensemble des rôles principaux** liste les cinq rôles principaux dans votre locataire en fonction du nombre total d’administrateurs permanents et éligibles pour chaque rôle. Le lien **Entreprendre une action** ouvre l’[Assistant PIM](pim-security-wizard.md) dans lequel vous pouvez convertir des administrateurs permanents en administrateurs éligibles par lots.

## <a name="pim-emails-for-azure-resource-roles"></a>E-mails PIM pour les rôles de ressources Azure

PIM envoie des e-mails aux propriétaires et administrateurs de l’accès utilisateur quand les événements suivants se produisent pour les rôles de ressources Azure :

- Quand une attribution de rôle est en attente d’approbation
- Quand un rôle est attribué
- Quand un rôle va bientôt expirer
- Quand un rôle peut être étendu
- Quand un rôle est renouvelé par un utilisateur final
- Quand une demande d’activation de rôle est terminée

PIM envoie des e-mails aux utilisateurs finaux quand les événements suivants se produisent pour les rôles de ressources Azure :

- Quand un rôle est attribué à l’utilisateur
- Quand le rôle d’un utilisateur a expiré
- Quand le rôle d’un utilisateur est étendu
- Quand la demande d’activation de rôle d’un utilisateur est terminée

L’exemple suivant montre un exemple d’e-mail envoyé quand un utilisateur se voit attribuer un rôle de ressource Azure pour l’organisation fictive Contoso.

![Nouvel e-mail PIM pour les rôles de ressources Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’annuaire Azure AD dans PIM](pim-how-to-change-default-settings.md)
- [Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM](azure-ad-pim-approval-workflow.md)
