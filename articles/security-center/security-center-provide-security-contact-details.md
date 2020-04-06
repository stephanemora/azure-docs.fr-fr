---
title: Fournir les détails du contact de sécurité dans Azure Security Center | Microsoft Docs
description: Ce document vous montre comment fournir les détails du contact de sécurité dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387816"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fournir les détails du contact de sécurité dans Azure Security Center
Azure Security Center vous recommande de fournir des coordonnées de sécurité pour votre abonnement Azure, si vous ne l’avez pas déjà fait. Ces informations seront être utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que vos données client ont été utilisées par un tiers illégal ou non autorisé. MSRC procède à une surveillance de la sécurité sur l'infrastructure et le réseau Azure et reçoit des plaintes concernant l’intelligence des menaces et des mauvaises utilisations de tiers.

Une notification par courrier électronique est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Les préférences de courrier électronique peuvent uniquement être configurées pour les stratégies d’abonnement. Les groupes de ressources d’un abonnement hériteront de ces paramètres. Les alertes sont disponibles uniquement dans le niveau Standard d’Azure Security Center.

Des notifications d’alertes par courrier électronique sont envoyées :
- À un seul destinataire de messagerie par type d’alerte et par jour  
- Pas plus de 3 messages par jour sont envoyés à un seul destinataire
- Chaque message contient une seule alerte, et non un ensemble d’alertes
- Uniquement pour les alertes de gravité élevée

> [!TIP]
> Pour les alertes avec d’autres niveaux de gravité, créez une [automatisation du workflow](workflow-automation.md) pour utiliser une application logique qui enverra des e-mails au personnel concerné.
 
Par exemple, si un message a été déjà envoyé pour vous alerter d’une attaque RDP, vous ne recevrez pas d’autre message concernant une attaque RDP le même jour, même si une autre alerte est déclenchée. 

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="set-up-email-notifications-for-alerts"></a>Configurer des notifications par e-mail pour les alertes <a name="email"></a>

1. Ouvrez la page **Notifications par e-mail** :

    - Pour les alertes, ouvrez **Tarification et paramètres**, sélectionnez l’abonnement approprié, puis **Notifications par e-mail**.

    - Si vous implémentez une recommandation, sous **Recommandations**, sélectionnez **Fournir les informations de contact de sécurité**, et sélectionnez l’abonnement Azure auquel vous souhaitez les ajouter. Les **Notifications par e-mail** s’ouvrent.

   ![Fournir des informations de contact de sécurité][2]

1. Entrez l’adresse e-mail du contact de sécurité. S’il y a plusieurs adresses, séparez-les par des virgules. Vous pouvez entrer autant d’adresses e-mail que vous voulez.

1. Pour recevoir des courriers électroniques concernant les alertes de gravité élevée, activez l’option **Send me emails about alerts**(M’envoyer des messages électroniques concernant les alertes). Pour les autres niveaux de gravité, utilisez une application logique, comme expliqué dans [Automatisation du workflow](workflow-automation.md).

1. Vous pouvez envoyer des notifications par e-mail aux propriétaires d’abonnements (administrateur de services fédérés classique et coadministrateurs, ainsi que le rôle Propriétaire RBAC à l’étendue de l’abonnement).

1. Pour appliquer les coordonnées de sécurité à votre abonnement, sélectionnez **Enregistrer**.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
