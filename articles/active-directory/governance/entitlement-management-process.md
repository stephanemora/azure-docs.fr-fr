---
title: Processus de demande et notifications par e-mail dans la gestion des droits d’utilisation d’Azure Active Directory - Azure Active Directory
description: Découvrez le processus de demande d’un package d’accès, et les envois de notifications par e-mail dans la gestion des droits d’utilisation Azure Active Directory.
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
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199935"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Processus de demande et notifications par e-mail dans la gestion des droits d’utilisation Azure AD

Lorsqu’un utilisateur envoie une demande à un package d’accès, un processus démarre pour remettre cette demande. La gestion des droits d’utilisation Azure AD envoie des notifications par e-mail aux approbateurs et demandeurs lorsque des événements clés se produisent pendant le processus. Cet article décrit le processus de demande et les notifications par e-mail qui sont envoyées.

## <a name="request-process"></a>Processus de demande

Un utilisateur qui a besoin d’accéder à un package d’accès peut soumettre une demande d’accès. Selon la configuration de la stratégie, la demande peut nécessiter une approbation. Lorsqu’une demande est approuvée, un processus commence à attribuer à l’utilisateur l’accès à chaque ressource dans le package d’accès. Le diagramme suivant montre une vue d’ensemble du processus et des différents états :

![Diagramme du processus d’approbation](./media/entitlement-management-process/request-process.png)

| State | Description |
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

Si vous êtes approbateur, des notifications par e-mail vous sont envoyées lorsque vous devez approuver une demande d’accès, et lorsqu’une demande d’accès a été validée. Si vous êtes demandeur, des notifications par e-mail indiquant l’état de votre demande vous sont envoyées.

Les diagrammes suivants montrent quand ces notifications par e-mail sont envoyées aux approbateurs ou au demandeur. Reportez-vous au [tableau des notifications par e-mail](entitlement-management-process.md#email-notifications-table) pour trouver le numéro correspondant dans les notifications par e-mail affichées dans les diagrammes.

### <a name="primary-approvers-and-alternate-approvers"></a>Approbateurs principaux et approbateurs de substitution
Le diagramme suivant illustre l’expérience des approbateurs principaux et des approbateurs de substitution, ainsi que les notifications par e-mail qu’ils reçoivent pendant le processus de demande :

![Déroulement du processus pour les approbateurs principaux et de substitution](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Demandeurs
Le diagramme suivant illustre l’expérience des demandeurs et les notifications par e-mail qu’ils reçoivent pendant le processus de demande :

![Déroulement du processus du demandeur](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tableau de notifications par e-mail
Le tableau suivant fournit plus de détails sur chacune de ces notifications par e-mail. Pour gérer ces e-mails, vous pouvez utiliser des règles. Par exemple, dans Outlook, vous pouvez créer des règles pour déplacer les e-mails vers un dossier si l’objet contient des mots de ce tableau :

| # | Objet de l’e-mail | Moment de l’envoi | Envoyé à |
| --- | --- | --- | --- |
| 1 | Action demandée : Approuver ou refuser une demande transférée d’ici le *[date]* | Cet e-mail sera envoyé aux approbateurs de phase 1 (une fois la demande remontée) pour agir. | Approbateur de substitution de phase 1 |
| 2 | Action demandée : Approuver ou refuser la demande d’ici le *[date]* | Cet e-mail sera envoyé aux approbateurs principaux de la phase 1, si la remontée est désactivée, pour leur demander d’agir. | Approbateur principal de phase 1 |
| 3 | Rappel : Approuver ou refuser la demande avant le *[date]* pour *[demandeur]* | Cet e-mail de rappel sera envoyé aux approbateurs principaux de la phase 1, si la remontée est désactivée, pour leur demander d’agir, seulement s’ils ne l’ont pas encore fait. | Approbateur principal de phase 1 |
| 4 | Approuver ou refuser la demande avant *[heure]* le *[date]* | Cet e-mail sera envoyé aux approbateurs principaux de la phase 1 (si la remontée est activée) pour leur demander d’agir. | Approbateur principal de phase 1 |
| 5\. | Rappel d’action requise : Approuver ou refuser la demande avant le *[date]* pour *[demandeur]* | Cet e-mail de rappel est envoyé aux approbateurs principaux de phase 1 si la remontée est activée, pour leur demander d’agir, uniquement lorsqu’ils n’ont pas encore pris de mesure. | Approbateur principal de phase 1 |
| 6 | La demande a expiré pour *[package d’accès]* | Cet e-mail est envoyé aux approbateurs principaux de phase 1 et/ou aux approbateurs de substitution de phase 1, pour une demande à phase unique ou plusieurs phases, une fois que la demande a expiré. | Approbateur principal de phase 1, approbateur alternatif de phase 1 |
| 7 | Demande approuvée pour *[demandeur]* à *[package d’accès]* | Cet e-mail est envoyé aux approbateurs principaux et/ou de substitution de phase 1 à la fin d’une demande. | Approbateur principal de phase 1, approbateur alternatif de phase 1 |
| 8 | Demande approuvée pour *[demandeur]* à *[package d’accès]* | Ce courrier électronique est envoyé aux approbateurs principaux de phase 1 et/ou aux approbateurs de substitution de phase 1, pour une demande à deux phases, seulement une fois que la phase 1 est approuvée. | Approbateur principal de phase 1, approbateur alternatif de phase 1 |
| 9 | Demande refusée pour *[package d’accès]* | Cet e-mail est envoyé au demandeur uniquement lorsque sa demande est refusée | Demandeur |
| 10 | Votre demande a expiré pour *[package d’accès]* | Cet e-mail est envoyé au demandeur à la fin de la phase 1, d’une demande à une ou plusieurs phases, une fois que la demande a expiré. | Demandeur |
| 18 | Vous avez maintenant accès à *[package d’accès]* | Cet e-mail est envoyé aux utilisateurs finaux pour leur indiquer qu’ils peuvent commencer à utiliser leur accès. | Demandeur |
| 19 | Extension d’accès pour *[package d’accès]* avant le *[date]* | Cet e-mail est envoyé aux utilisateurs finaux avant l’expiration de leur accès. | Demandeur |
| 20 | L’accès est terminé pour *[package d’accès]* | Cet e-mail est envoyé aux utilisateurs finaux après l’expiration de leur accès. | Demandeur |

### <a name="access-request-emails"></a>E-mails notifiant la demande d’accès

Lorsqu’un demandeur envoie une demande d’accès à un package d’accès qui est configuré pour demander l’approbation, tous les approbateurs ajoutés à la stratégie reçoivent une notification par e-mail avec les détails de la demande. Les détails comprennent le nom du demandeur, l’organisation, la date de début et de fin le cas échéant, la justification professionnelle, la date de soumission de la demande et la date d’expiration de la demande.

L’e-mail comporte un lien, sur lequel les approbateurs peuvent cliquer pour accéder à Mon accès et approuver ou refuser la demande d’accès. Voici un exemple de notification par e-mail qui est envoyé à un approbateur lorsqu’un demandeur soumet une demande d’accès :

![E-mail concernant l’approbation de la demande pour un package d’accès](./media/entitlement-management-shared/approver-request-email.png)

Les approbateurs principaux reçoivent également une notification par e-mail contenant un rappel pour prendre des mesures et prendre une décision pour la demande. Voici un exemple d’e-mail de notification que les approbateurs principaux reçoivent pour leur rappeler de prendre des mesures :

![E-mail de rappel notifiant la demande d’accès](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Autres e-mails de demande d’approbateur

Si le transfert vers d’autres approbateurs est activé, selon la stratégie de transfert, et si la demande est toujours en attente, la demande est transférée. L’approbateur de substitution reçoit un e-mail de notification pour approuver ou refuser la demande. Voici un exemple d’e-mail de notification que les approbateurs de substitution reçoivent :

![E-mail de demande à l’approbateur de substitution](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Les approbateurs principaux et de substitution peuvent approuver ou refuser la demande.

### <a name="approved-or-denied-emails"></a>E-mails notifiant l’approbation ou le refus

Les demandeurs sont avertis lorsque leur demande d’accès est approuvée et accessible, ou lorsqu’elle est refusée. Quand un approbateur reçoit une demande d’accès présentée par un demandeur, il peut approuver ou refuser la demande d’accès. L’approbateur doit ajouter une justification professionnelle motivant sa décision. Voici un exemple d’e-mail envoyé aux approbateurs principaux ou de substitution après l’approbation d’une demande :

![Vérification de l’e-mail de demande d’accès](./media/entitlement-management-process/approver-request-email-approved.png)

Lorsqu’une demande d’accès est approuvée et que son accès est configuré, une notification par e-mail est envoyée au demandeur pour lui indiquer qu’il a à présent accès au package d’accès. Voici un exemple de notification par e-mail qui est envoyé à un demandeur lorsque l’accès à un package d’accès lui est accordé :

![E-mail notifiant la demande d’accès expirée](./media/entitlement-management-process/requestor-email-approved.png)

Lorsqu’une demande d’accès est refusée, une notification par e-mail est envoyée au demandeur. Voici un exemple de notification par e-mail qui est envoyé à un demandeur lorsque sa demande d’accès est refusée :

![E-mail de refus de la demande du demandeur](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>E-mails notifiant la demande d’accès expirée

Les demandes d’accès peuvent expirer si aucun approbateur n’a approuvé ou refusé la demande. 

Lorsque la demande atteint sa date d’expiration, elle expire et ne peut plus être approuvée ou refusée par les approbateurs. Voici un exemple d’e-mail de notification envoyé à tous les approbateurs principaux et de substitution :

 ![E-mail notifiant la demande d’accès expirée pour les approbateurs](./media/entitlement-management-process/approver-request-email-expired.png)

 Une notification par e-mail est également envoyée au demandeur pour l’informer que sa demande d’accès a expiré et qu’il doit soumettre sa demande à nouveau. Voici un exemple de notification par e-mail qui est envoyée à un demandeur lorsque sa demande d’accès a expiré :

![E-mail notifiant la demande d’accès expirée pour le demandeur](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Étapes suivantes

- [Demander l’accès à un package d’accès](entitlement-management-request-access.md)
- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)
