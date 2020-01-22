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
ms.openlocfilehash: 1a66ea200082f60a3a763c6a4e2bdea62ec473d8
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921000"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fournir les détails du contact de sécurité dans Azure Security Center
Azure Security Center vous recommande de fournir les détails du contact de sécurité pour votre abonnement Azure, si vous ne l’avez pas déjà fait. Ces informations seront être utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que vos données client ont été utilisées par un tiers illégal ou non autorisé. MSRC procède à une surveillance de la sécurité sur l'infrastructure et le réseau Azure et reçoit des plaintes concernant l’intelligence des menaces et des mauvaises utilisations de tiers.

Une notification par courrier électronique est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Les préférences de courrier électronique peuvent uniquement être configurées pour les stratégies d’abonnement. Les groupes de ressources d’un abonnement hériteront de ces paramètres. Les alertes sont disponibles uniquement dans le niveau Standard d’Azure Security Center.

Des notifications d’alertes par courrier électronique sont envoyées :
- Uniquement pour les alertes de gravité élevée
- À un seul destinataire de messagerie par type d’alerte et par jour  
- Pas plus de 3 messages par jour sont envoyés à un seul destinataire
- Chaque message contient une seule alerte, et non un ensemble d’alertes
 
Par exemple, si un message a été déjà envoyé pour vous alerter d’une attaque RDP, vous ne recevrez pas d’autre message concernant une attaque RDP le même jour, même si une autre alerte est déclenchée. 

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## Configurer des notifications par e-mail pour les alertes <a name="email"></a>

1. Dans le portail, sélectionnez **Tarification et paramètres**.
1. Cliquez sur l'abonnement.
1. Cliquez sur **Notifications par e-mail**.

> [!NOTE]
> Si vous implémentez une recommandation, sous **Recommandations**, sélectionnez **Fournir les informations de contact de sécurité**, et sélectionnez l’abonnement Azure auquel vous souhaitez les ajouter. Les **Notifications par e-mail** s’ouvrent.

   ![Fournir des informations de contact de sécurité][2]

   * Entrez l’adresse e-mail du contact de sécurité. S’il y a plusieurs adresses, séparez-les par des virgules. Vous pouvez entrer le nombre d’adresses e-mail que vous voulez.
   * Entrez le numéro de téléphone international du contact de sécurité.
   * Pour recevoir des courriers électroniques concernant les alertes de gravité élevée, activez l’option **Send me emails about alerts**(M’envoyer des messages électroniques concernant les alertes).
   * Vous avez la possibilité d’envoyer des notifications par courrier électronique aux propriétaires d’abonnements (administrateur de service classique et coadministrateurs, ainsi qu’au rôle de propriétaire RBAC à l’étendue de l’abonnement).
   * Sélectionnez **Enregistrer** pour appliquer les informations du contact de sécurité à votre abonnement.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
