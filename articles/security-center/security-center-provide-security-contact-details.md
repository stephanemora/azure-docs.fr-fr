---
title: Fournir les détails du contact de sécurité dans Azure Security Center | Microsoft Docs
description: Ce document vous montre comment fournir les détails du contact de sécurité dans Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/9/2018
ms.author: rkarlin
ms.openlocfilehash: 3c2da9830a8ddbce842d959ba086bf4eb30d5fdb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258762"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fournir les détails du contact de sécurité dans Azure Security Center
Azure Security Center vous recommande de fournir les détails du contact de sécurité pour votre abonnement Azure, si vous ne l’avez pas déjà fait. Ces informations seront être utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que vos données client ont été utilisées par un tiers illégal ou non autorisé. MSRC procède à une surveillance de la sécurité sur l'infrastructure et le réseau Azure et reçoit des plaintes concernant l’intelligence des menaces et des mauvaises utilisations de tiers.

Une notification par courrier électronique est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Les préférences de courrier électronique peuvent uniquement être configurées pour les stratégies d’abonnement. Les groupes de ressources d’un abonnement hériteront de ces paramètres. 

Des notifications d’alertes par courrier électronique sont envoyées :
- Uniquement pour les alertes de gravité élevée
- À un seul destinataire de messagerie par type d’alerte et par jour  
- Pas plus de 3 messages par jour sont envoyés à un seul destinataire
- Chaque message contient une seule alerte, et non un ensemble d’alertes
 
Par exemple, si un message a été déjà envoyé pour vous alerter d’une attaque RDP, vous ne recevrez pas d’autre message concernant une attaque RDP le même jour, même si une autre alerte est déclenchée. 
 

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Sous **Recommandations**, sélectionnez **Fournissez les détails du contact de sécurité**.
   ![Fournir le contact de sécurité][1]
2. Sélectionnez l’abonnement Azure pour lequel fournir les informations du contact.
3. Les **Notifications par e-mail** s’ouvrent.

   ![Fournir des informations de contact de sécurité][2]

   * Entrez l’adresse e-mail du contact de sécurité. S’il y a plusieurs adresses, séparez-les par des virgules. Vous pouvez entrer le nombre d’adresses e-mail que vous voulez.
   * Entrez le numéro de téléphone international du contact de sécurité.
   * Pour recevoir des courriers électroniques concernant les alertes de gravité élevée, activez l’option **Send me emails about alerts**(M’envoyer des messages électroniques concernant les alertes).
   * À l’avenir, vous aurez la possibilité d’envoyer des notifications par courrier électronique aux propriétaires de l’abonnement. Cette option est actuellement grisée.
   * Sélectionnez **Enregistrer** pour appliquer les informations du contact de sécurité à votre abonnement.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
