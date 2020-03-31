---
title: Renforcez votre sécurité avec Azure Security Center | Microsoft Docs
description: Cet article a pour but de vous aider à renforcer votre sécurité avec la surveillance de vos ressources dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603773"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Renforcez votre sécurité avec Azure Security Center
Cet article vous aide à renforcer votre sécurité. Utilisez les fonctionnalités de surveillance d’Azure Security Center pour vous assurer que la sécurité de votre ressource est aussi efficace que possible et contrôler la conformité aux stratégies.

## <a name="how-do-you-strengthen-your-security-posture"></a>Comment renforcer votre sécurité avec Azure ?
Nous pensons souvent que la surveillance consiste à veiller et à attendre qu’un événement se produise pour y répondre. Le renforcement de votre sécurité fait référence à une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques.

Une fois que vous avez activé les [stratégies de sécurité](tutorial-security-policy.md) pour les ressources d’un abonnement, Azure Security Center analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Les informations sur la configuration du réseau sont instantanément disponibles. Selon le nombre de machines virtuelles et d’ordinateurs sur lesquels est installé l’agent, il se peut que la collecte d’informations sur la configuration des ordinateurs et des machines virtuelles (statut des mises à jour de sécurité et configuration du système d’exploitation) ne soit disponible qu’au bout d’une heure, voire plus. Vous pouvez afficher une liste complète des problèmes et des méthodes permettant de renforcer votre réseau et de résoudre ces risques dans la partie **Recommandations**.

Vous pouvez visualiser l’état de sécurité de vos ressources et les problèmes éventuels par type de ressource :

- Pour surveiller l’intégrité des ressources de votre ordinateur et de vos applications et recevoir des recommandations visant à améliorer leur sécurité, consultez [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
- Pour analyser vos ressources réseau, telles que les machines virtuelles, les groupes de sécurité réseau et les points de terminaison, et recevoir des recommandations visant à améliorer leur sécurité, consultez [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md) pour en savoir plus. 
- Pour surveiller vos données et vos ressources de stockage, telles que les serveurs SQL et les comptes de stockage, et recevoir des recommandations visant à améliorer leur sécurité, consultez [Protection des données et du service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md) pour en savoir plus. 
- Pour surveiller vos ressources d’identités et d’accès, notamment les authentifications multifacteur et les autorisations de compte, et recevoir des recommandations visant à améliorer leur sécurité, consultez [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md) pour en savoir plus. 
- Pour surveiller l’accès juste-à-temps à vos ressources, consultez [Gérer l’accès juste-à-temps à la machine virtuelle](security-center-just-in-time.md) pour en savoir plus. 


Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).



![Mosaïque Intégrité de la sécurité des ressources](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez vu comment utiliser les fonctionnalités de surveillance d’Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des paramètres de sécurité dans Azure Security Center.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.