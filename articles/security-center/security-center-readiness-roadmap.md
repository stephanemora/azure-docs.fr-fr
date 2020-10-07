---
title: Feuille de route de préparation de Azure Security Center | Microsoft Docs
description: Ce document vous fournit une feuille de route de préparation pour démarrer sur Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 52ea6f862b7ef6190348743a128912131e6a9609
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314485"
---
# <a name="azure-security-center-readiness-roadmap"></a>Feuille de route de préparation pour Azure Security Center
Ce document fournit une feuille de route de préparation qui vous aidera à prendre en main Azure Security Center.

## <a name="understanding-security-center"></a>Comprendre Security Center
Azure Security Center fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces pour les charges de travail s’exécutant dans Azure, en local et dans d’autres clouds. 

Utilisez les ressources suivantes pour prendre en main Security Center.

Articles
- [Présentation d’Azure Security Center](security-center-introduction.md)
- [Guide de démarrage rapide de Azure Security Center](security-center-get-started.md)

Videos
- [Vidéo de présentation rapide](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Vue d’ensemble des fonctionnalités de prévention, de détection et de réponse de Security Center](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planification et fonctionnement

Pour tirer pleinement parti du Centre de sécurité, il est important de comprendre comment les différents utilisateurs ou équipes de votre entreprise utilisent ce service afin de répondre aux besoins de sécurisation des opérations, de surveillance, de gouvernance et de réponse aux incidents.

Utilisez les ressources suivantes pour vous aider lors des processus de planification et de fonctionnement.

- [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Intégration des ordinateurs à Security Center
Security Center détecte automatiquement les abonnements et espaces de travail Azure non protégés par Azure Defender, à savoir les abonnements Azure utilisant Security Center Gratuit et les espaces de travail dans lesquels la solution de sécurité n’est pas activée.

Utilisez les ressources suivantes pour vous aider lors des processus d’intégration.

- [Intégrer des ordinateurs autres qu’Azure](quickstart-onboard-machines.md)
- [Vue d’ensemble de Azure Security Center hybride](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Atténuation des problèmes de sécurité à l’aide de Security Center
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs.

Utilisez les ressources suivantes pour vous aider à gérer les alertes de sécurité et à protéger vos ressources.

Articles    
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
- [Protection de votre réseau dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
- [Protection des données et du service SQL Azure dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Vidéo    
- [Atténuation des problèmes de sécurité à l’aide de Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center pour la réponse aux incidents
Pour réduire les coûts et les dommages, il est important de disposer d’un plan de réponse aux incidents avant qu’une attaque ne survienne. Vous pouvez utiliser Azure Security Center à différentes étapes de la réponse à un incident.

Utilisez les ressources suivantes pour comprendre comment Security Center peut être intégré dans votre processus de réponse aux incidents.

Videos    
* [Azure Security Center dans la réponse aux incidents](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Répondre rapidement aux menaces avec des opérations et des investigations de sécurité de nouvelle génération](https://youtu.be/e8iFCz5RM4g)

Articles    
* [Utilisation d’Azure Security Center pour la réponse aux incidents](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatiser la réponse avec l’automatisation des worflows](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Défense de cloud avancée

Les machines virtuelles Azure peuvent profiter des fonctionnalités de défense de cloud avancée dans Security Center. Ces fonctionnalités incluent l’accès juste-à-temps à la machine virtuelle ainsi que des contrôles d’application adaptatifs.

Utilisez les ressources suivantes pour savoir comment utiliser ces fonctionnalités dans Security Center.

Videos    
* [Azure Security Center – Accès juste-à-temps à la machine virtuelle](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - Contrôles d’application adaptative](https://youtu.be/wWWekI1Y9ck)

Articles    
* [Gérer l’accès juste-à-temps aux machines virtuelles](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Contrôles d’application adaptative dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Travaux pratiques

* [Atelier pratique du Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook de recommandations du pare-feu d’applications web (WAF) dans Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Playbook Azure Security Center : alertes de sécurité](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Page de documentation de Security Center](https://docs.microsoft.com/azure/security-center/)
* [Page de documentation de l’API REST de Security Center](https://msdn.microsoft.com/library/mt704034.aspx)
* [FAQ de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Page de tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Meilleures pratiques relatives à la sécurité d’identité](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Meilleures pratiques en matière de sécurité réseau](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [Recommandations de PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Conformité](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Les clients Log Analytics peuvent désormais utiliser Azure Security Center pour protéger leurs charges de travail de Cloud hybride](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Ressources de la Communauté

* [UserVoice de Security Center](https://feedback.azure.com/forums/347535-azure-security-center)
* [Page de questions et réponses pour Security Center](https://docs.microsoft.com/answers/topics/azure-security-center.html)