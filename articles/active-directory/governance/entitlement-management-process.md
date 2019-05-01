---
title: Demande de processus et les notifications électroniques dans Gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: En savoir plus sur le processus de demande pour un package d’accès, et lorsque les notifications par courrier électronique sont envoyées dans la gestion des droits Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873649"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Demande de processus et les notifications électroniques dans Gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Lorsqu’un utilisateur soumet une demande à un package de l’accès, un processus est démarré pour fournir cette demande. Gestion des droits Azure AD envoie également des notifications par courrier électronique des approbateurs et des demandeurs lorsque des événements clés se produisent pendant le processus.

Cet article décrit le processus de demande et les notifications par courrier électronique qui sont envoyées.

## <a name="request-process"></a>Processus de demande

Un utilisateur qui a besoin d’accéder à un package d’accès peut soumettre une demande d’accès. Selon la configuration de la stratégie, la demande peut nécessiter une approbation. Lorsqu’une demande est approuvée, un processus commence à affecter l’accès utilisateur à chaque ressource dans le package de l’accès. Le diagramme suivant montre une vue d’ensemble du processus et les différents états.

![Diagramme du processus d’approbation](./media/entitlement-management-process/request-process.png)

| État | Description |
| --- | --- |
| Envoyé | Utilisateur soumet une demande. |
| Approbation en attente | Si la stratégie pour un package d’accès exige une approbation, parcours d’une demande pour en attente d’approbation. |
| Expiré | Si aucun approbateur n’examinez une demande dans le délai d’expiration de la demande d’approbation, la demande expire. Pour réessayer, l’utilisateur devra soumettre à nouveau leur demande. |
| Refusé | Approbateur refuse une requête. |
| Approved | Approbateur approuve une demande. |
| Remise | Utilisateur a **pas** été affecté un accès à toutes les ressources dans le package de l’accès. S’il s’agit d’un utilisateur externe, l’utilisateur n’a pas encore accessible de répertoire de ressources et accepté les autorisations de l’invite de commandes. |
| Delivered (Livrée) | Utilisateur a reçu l’accès à toutes les ressources dans le package de l’accès. |
| Accéder aux étendues | Si les extensions sont autorisées dans la stratégie, l’utilisateur étendu l’attribution. |
| Accès expiré | L’accès utilisateur pour le package de l’accès a expiré. Pour accéder à nouveau, l’utilisateur devra soumettre une demande. |

## <a name="email-notifications"></a>Notifications par e-mail

Si vous êtes un approbateur, vous sont envoyées les notifications par courrier électronique lorsque vous avez besoin d’approuver une demande d’accès et quand une demande d’accès a été effectuée. Si vous êtes un demandeur, vous sont envoyées les notifications par courrier électronique qui indiquent l’état de votre demande. Le diagramme suivant illustre lorsque ces notifications par courrier électronique est envoyé.

![Processus de gestion des droits e-mail](./media/entitlement-management-process/email-notifications.png)

Le tableau suivant fournit plus de détails sur chacune de ces notifications par courrier électronique.

| # | Objet de l’e-mail | Lors de l’envoi | Envoyé à |
| --- | --- | --- | --- |
| 1 | Action requise : Demande de révision d’accès à partir de *[demandeur]* à *[package d’accès]* par *[date]* | Quand un demandeur envoie une demande pour un package d’accès | Tous les approbateurs |
| 2 | Action requise : Demande de révision d’accès à partir de *[demandeur]* à *[package d’accès]* par *[date]* | Délai de demande de X jours avant l’approbation | Tous les approbateurs |
| 3 | Notification d’état : *[demandeur]* de demande d’accès à *[package d’accès]* a expiré | Lorsque les approbateurs de ne pas approuver ou refuser une demande d’accès au sein de la durée de la demande | Demandeur |
| 4 | Notification d’état : *[demandeur]* demande d’accès à *[package d’accès]* a été effectuée | Lorsque la première approbateur approuve ou refuse une demande d’accès | Tous les approbateurs |
| 5. | Vous avez été refusé l’accès à *[package d’accès]* | Quand un demandeur n’a pas accès au package d’accès | Demandeur |
| 6. | Vous avez maintenant accès à *[package d’accès]*  | Lorsqu’un demandeur a accès à toutes les ressources dans le package de l’accès | Demandeur |
| 7 | Votre accès à *[package d’accès]* expire dans X jours | Expiration de X jours avant l’accès au package d’accès du demandeur | Demandeur |
| 8 | Votre accès à *[package d’accès]* a expiré | Lorsque l’accès du demandeur à un package d’accès arrive à expiration | Demandeur |

### <a name="review-access-request-emails"></a>E-mails de demande de révision accès

Lorsqu’un demandeur envoie une demande d’accès pour un package d’accès qui est configuré pour exiger l’approbation, tous les approbateurs configurés dans la stratégie de recevoir une notification par courrier électronique avec les détails de la demande. Détails incluent le nom du demandeur, organisation, date de début et de fin s’il est fourni, justification professionnelle, lorsque la demande a été soumise et date d’expiration de la demande d’accès. Cet e-mail inclut un lien où les approbateurs peuvent approuver ou refuser la demande d’accès. Voici un exemple de notification de courrier électronique est envoyé à un approbateur lorsqu’un demandeur envoie une demande d’accès.

![E-mail de demande d’accès révision](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>E-mails approuvées ou refusées

Demandeurs sont avertis lorsque leur demande d’accès est approuvé et disponibles pour l’accès, ou lorsque leur demande d’accès est refusé. Lorsqu’un approbateur examine une demande d’accès présentée par un demandeur, ils peuvent approuver ou refuser la demande d’accès. L’approbateur doit ajouter une justification pour leur décision.

Lorsqu’une demande d’accès est approuvée, gestion des habilitations démarre le processus d’octroi au demandeur un accès à chacune des ressources dans le package de l’accès. Une fois que le demandeur a accès à toutes les ressources dans le package de l’accès, une notification par courrier électronique est envoyée au demandeur que sa demande d’accès a été approuvée et qu’ils ont accès au package d’accès. Voici un exemple de notification de courrier électronique est envoyé à un demandeur lors de l’accès à un package de l’accès leur est accordé.

Lorsqu’une demande d’accès est refusée, une notification par courrier électronique est envoyée au demandeur. Voici un exemple de notification de courrier électronique est envoyé au demandeur lorsque leur demande d’accès est refusé.

### <a name="expired-access-request-emails"></a>Expiration des e-mails de demande d’accès

Demandeurs sont avertis lorsque leur demande d’accès a expiré. Lorsqu’un demandeur envoie une demande d’accès, la requête a une durée après laquelle il arrive à expiration. S’il n’existe aucun approbateur qui soumettent une décision d’approuver ou refuser, la demande continue à rester dans un état en attente d’approbation. Lorsque la demande atteint sa durée d’expiration configuré, la demande arrive à expiration et peut ne plus être approuvée ou refusée par les approbateurs. Dans ce cas, la demande s’exécute dans un état expiré. Une requête expirée peut ne plus être approuvée ou refusée. Une notification par courrier électronique est envoyée au demandeur qui leur demande d’accès a expiré, et qu’il doit renvoyer la demande d’accès. Voici un exemple de notification de courrier électronique est envoyé au demandeur lorsque leur demande d’accès a expiré.

![Accéder à la messagerie demande arrivé à expiration](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Étapes suivantes

- [Demander l’accès à un package d’accès](entitlement-management-request-access.md)
- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)
