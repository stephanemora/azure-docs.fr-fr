---
title: Processus de demande et notifications par e-mail dans la gestion des droits d’utilisation d’Azure Active Directory (préversion) - Azure Active Directory
description: Découvrez le processus de demande d’un package d’accès, et les envois de notifications par e-mail dans la gestion des droits d’utilisation Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb5f322d8bc974274f7f2da7811b124499224635
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678142"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Processus de demande et notifications par e-mail dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Lorsqu’un utilisateur envoie une demande à un package d’accès, un processus est démarré pour remettre cette demande. La gestion des droits d’utilisation Azure AD envoie également des notifications par e-mail aux approbateurs et demandeurs lorsque des événements clés se produisent pendant le processus.

Cet article décrit le processus de demande et les notifications par e-mail qui sont envoyées.

## <a name="request-process"></a>Processus de demande

Un utilisateur qui a besoin d’accéder à un package d’accès peut soumettre une demande d’accès. Selon la configuration de la stratégie, la demande peut nécessiter une approbation. Lorsqu’une demande est approuvée, un processus commence à attribuer à l’utilisateur l’accès à chaque ressource dans le package d’accès. Le diagramme suivant montre une vue d’ensemble du processus et des différents états.

![Diagramme du processus d’approbation](./media/entitlement-management-process/request-process.png)

| État | Description |
| --- | --- |
| Envoyée | L’utilisateur soumet une demande. |
| Approbation en attente | Si la stratégie d’un package d’accès exige une approbation, la demande passe à l’approbation en attente. |
| Expiré | Si aucun approbateur ne valide la demande dans le délai d’attente de la demande d’approbation, la demande expire. Pour réessayer, l’utilisateur doit soumettre de nouveau sa demande. |
| Refusée | L’approbateur refuse une demande. |
| Approved | L’approbateur approuve une demande. |
| Remise | L’accès à toutes les ressources dans le package d’accès n’a **pas** été affecté à l’utilisateur. S’il s’agit d’un utilisateur externe, il n’a peut-être pas encore accédé au répertoire de ressources ni accepté l’invite de consentement. |
| Delivered (Livrée) | L’utilisateur a reçu l’accès à toutes les ressources dans le package d’accès. |
| Accès étendu | Si des extensions sont autorisées dans la stratégie, l’utilisateur a étendu l’attribution. |
| Accès expiré | L’accès de l’utilisateur au package d’accès a expiré. Pour obtenir de nouveau l’accès, l’utilisateur doit soumettre une demande. |

## <a name="email-notifications"></a>Notifications par e-mail

Si vous êtes approbateur, des notifications par e-mail vous sont envoyées lorsque vous devez approuver une demande d’accès, et lorsqu’une demande d’accès a été validée. Si vous êtes demandeur, des notifications par e-mail indiquant l’état de votre demande vous sont envoyées. Le diagramme suivant montre quand ces notifications par e-mail sont envoyées.

![Parcours des e-mails de la gestion des droits d’utilisation](./media/entitlement-management-process/email-notifications.png)

Le tableau suivant fournit plus de détails sur chacune de ces notifications par e-mail.

| # | Objet de l’e-mail | Moment de l’envoi | Envoyé à |
| --- | --- | --- | --- |
| 1 | Action demandée : examiner la demande d’accès de *[demandeur]* à *[package d’accès]* avant le *[date]* | Quand un demandeur soumet une demande pour un package d’accès | Tous les approbateurs |
| 2 | Action demandée : examiner la demande d’accès de *[demandeur]* à *[package d’accès]* avant le *[date]* | X jours avant l’expiration de la demande d’approbation | Tous les approbateurs |
| 3 | Notification d’état : la demande d’accès de *[demandeur]* à *[package d’accès]* a expiré | Quand les approbateurs n’approuvent pas ou qu’ils refusent une demande d’accès dans le délai imparti de la demande | Demandeur |
| 4 | Notification d’état : la demande d’accès de *[demandeur]* à *[package d’accès]* a été validée | Quand le premier approbateur approuve ou refuse une demande d’accès | Tous les approbateurs |
| 5\. | L’accès à *[package d’accès]* vous a été refusé. | Quand l’accès au package d’accès a été refusé à un demandeur | Demandeur |
| 6 | Vous avez maintenant accès à *[package d’accès]*  | Quand un demandeur a accès à toutes les ressources dans le package d’accès | Demandeur |
| 7 | Votre accès à *[package d’accès]* expire dans X jour(s) | X jours avant que l’accès du demandeur au package d’accès expire | Demandeur |
| 8 | Votre accès à *[package d’accès]* a expiré | Quand l’accès du demandeur à un package d’accès expire | Demandeur |

### <a name="access-request-emails"></a>E-mails notifiant la demande d’accès

Lorsqu’un demandeur envoie une demande d’accès à un package d’accès qui est configuré pour demander l’approbation, tous les approbateurs définis dans la stratégie reçoivent une notification par e-mail avec les détails de la demande. Les détails comprennent le nom du demandeur, l’organisation, la date de début et de fin le cas échéant, la justification professionnelle, la date de soumission de la demande et la date d’expiration de la demande. L’e-mail comporte un lien, dans lequel les approbateurs peuvent approuver ou refuser la demande d’accès. Voici un exemple de notification par e-mail qui est envoyé à un approbateur lorsqu’un demandeur soumet une demande d’accès.

![Vérification de l’e-mail de demande d’accès](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>E-mails notifiant l’approbation ou le refus

Les demandeurs sont avertis lorsque leur demande d’accès est approuvée et accessible, ou lorsqu’elle est refusée. Quand un approbateur reçoit une demande d’accès présentée par un demandeur, il peut approuver ou refuser la demande d’accès. L’approbateur doit ajouter une justification professionnelle motivant sa décision.

Lorsqu’une demande d’accès est approuvée, la gestion des droits d’utilisation démarre le processus d’octroi au demandeur d’un accès à chacune des ressources dans le package d’accès. Une fois que le demandeur a obtenu l’accès à chacune des ressources du package d’accès, une notification par e-mail est envoyée au demandeur l’informant que sa demande d’accès a été approuvée et qu’il a maintenant accès au package d’accès. Voici un exemple de notification par e-mail qui est envoyé à un demandeur lorsque l’accès à un package d’accès lui est accordé.

Lorsqu’une demande d’accès est refusée, une notification par e-mail est envoyée au demandeur. Voici un exemple de notification par e-mail qui est envoyé à un demandeur lorsque sa demande d’accès est refusée.

### <a name="expired-access-request-emails"></a>E-mails notifiant la demande d’accès expirée

Les demandeurs sont avertis lorsque leur demande d’accès a expiré. Lorsqu’un demandeur envoie une demande d’accès, la requête présente un délai au bout duquel elle expire. S’il n’y a aucun approbateur pour envoyer une décision d’approbation/de refus, la demande est maintenue dans un état d’approbation en attente. Lorsque la demande atteint son délai d’expiration configuré, elle expire et ne peut plus être approuvée ou refusée par les approbateurs. Dans ce cas, la demande prend l’état expiré. Une requête expirée ne peut plus être approuvée ou refusée. Une notification par e-mail est envoyée au demandeur l’informant que sa demande d’accès a expiré et qu’il doit soumettre sa demande à nouveau. Voici un exemple de notification par e-mail qui est envoyée à un demandeur lorsque sa demande d’accès a expiré.

![E-mail notifiant la demande d’accès expirée](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Étapes suivantes

- [Demander l’accès à un package d’accès](entitlement-management-request-access.md)
- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)
