---
title: Présentation du service
description: Découvrez-en plus sur les services et les fonctionnalités de Defender pour IoT, puis comprenez comment Defender pour IoT offre une sécurité IoT complète.
ms.topic: overview
ms.date: 12/09/2020
ms.openlocfilehash: 15772ee21587ed2bc010e31174af6daac71dfc12
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786836"
---
# <a name="welcome-to-azure-defender-for-iot"></a>Bienvenue sur Azure Defender pour IoT

Les réseaux de technologie opérationnelle (OT) alimentent un grand nombre des aspects les plus critiques de notre société. Toutefois, un grand nombre de ces technologies n’ont pas été conçues en tenant compte de la sécurité et ne peuvent pas être protégées par des contrôles de sécurité informatique traditionnels. Pendant ce temps, l’Internet des objets (IoT) donne lieu à une nouvelle vague d’innovations avec des milliards d’appareils connectés, ce qui a pour effet d’augmenter la surface et les risques d’attaque.  

Azure Defender pour IoT est une solution de sécurité unifiée pour l’identification des appareils, des vulnérabilités et des menaces de l’IoT/OT. Elle vous permet de sécuriser l’ensemble de votre environnement IoT/OT, que vous ayez besoin de protéger des appareils IoT/OT existants ou de renforcer la sécurité de nouvelles innovations IoT.  

Azure Defender pour IoT propose deux ensembles de capacités pour répondre aux besoins de votre environnement.

Pour les organisations d’utilisateurs finaux ayant des environnements IoT/OT, Azure Defender pour IoT fournit une analyse de couche réseau sans agent qui :

- Peut être déployée rapidement.
- S’intègre facilement à divers équipements industriels et outils SOC.
- N’a aucun impact sur les performances ou la stabilité du réseau IoT/OT. 

La plateforme peut être entièrement déployée localement ou dans des environnements hybrides et connectés à Azure.  

Pour les fabricants d’appareils IoT, Azure Defender pour IoT propose également un micro-agent léger qui prend en charge les systèmes d’exploitation IoT standard, tels que Linux et RTOS. Cet agent léger permet de garantir que la sécurité est intégrée à vos initiatives IoT/OT, de la périphérie au cloud. Il comprend le code source pour un déploiement flexible et personnalisable. 

## <a name="agentless-solution-for-organizations"></a>Solution sans agent pour les organisations 

Les appareils IoT et OT plus anciens ne prennent pas en charge les agents et sont souvent non corrigés, mal configurés et invisibles pour les équipes informatiques. Ces qualités en font des cibles faciles pour les personnes à l’origine de la menace qui veulent s’infiltrer plus profondément dans les réseaux d’entreprise. 

Les outils traditionnels de surveillance de la sécurité réseau développés pour les réseaux informatiques d’entreprise ne peuvent pas traiter ces environnements, car ils ne comprennent pas bien les protocoles spécialisés, les appareils et les comportements de machine à machine (M2M) des environnements IoT et OT. 

Les capacités d’analyse sans agent d’Azure Defender pour IoT vous offrent une visibilité et une sécurité pour ces réseaux. Vous pouvez ainsi résoudre les problèmes clés de ces environnements. 

### <a name="automatic-device-discovery"></a>Détection automatique des appareils  

Utilisez l’analyse passive et sans agent du réseau pour obtenir un inventaire complet de l’ensemble de vos appareils IoT/OT, de leurs détails et de la façon dont elles communiquent, sans aucun impact sur le réseau IoT/OT.  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>Visibilité proactive des risques et des vulnérabilités
 
Identifiez les risques et les vulnérabilités dans votre environnement IoT/OT. Par exemple, identifiez les appareils non corrigés, les ports ouverts et les applications et connexions non autorisées. Vous pouvez également identifier les modifications apportées aux configurations des appareils, au code PLC et au microprogramme. 

### <a name="iotot-threat-detection"></a>Détection des menaces IoT/OT  

Détectez les activités anormales ou non autorisées grâce à un renseignement sur les menaces et à une analyse comportementale spécialisés tenant compte de l’IoT/OT. Vous pouvez même détecter les menaces avancées ignorées par les IOC statiques, comme les programmes malveillants zero-day, les programmes malveillants sans fichier et les tactiques « Living off the land ». 

### <a name="unified-security-management-across-iotot"></a>Gestion unifiée de la sécurité dans l’IoT/OT

Intégrez Azure Sentinel pour avoir une vue d’ensemble de votre organisation. Implémentez une gouvernance unifiée de la sécurité de l’IoT/OT avec intégration à vos workflows existants, notamment des outils tiers tels que Splunk, IBM QRadar et ServiceNow. 

## <a name="agent-based-solution-for-device-builders"></a>Solution basée sur un agent pour les fabricants d’appareils 

La sécurité est une préoccupation quasi-universelle pour les responsables de l’implémentation de l’IoT. Les appareils IoT ont des besoins uniques en matière de surveillance des points de terminaison, de gestion de la posture de sécurité et de détection des menaces, le tout avec des exigences de performances très spécifiques. 

Les agents de sécurité Azure Defender pour IoT vous permettent d’intégrer la sécurité directement dans vos nouveaux appareils IoT et projets Azure IoT. Le micro-agent dispose d’options de déploiement souples, notamment la possibilité de le déployer sous forme de package binaire ou de modifier le code source. Le micro-agent est également disponible pour les systèmes d’exploitation IoT standard tels que Linux et Azure RTOS.  

Le micro-agent Azure Defender pour IoT fournit une visibilité sur les points de terminaison pour la gestion de la posture de sécurité, la détection des menaces et l’intégration aux autres outils de sécurité de Microsoft pour une gestion unifiée de la sécurité. 

### <a name="security-posture-management"></a>Gestion de la posture de sécurité

Surveillez de manière proactive la posture de sécurité de vos appareils IoT. Azure Defender pour IoT fournit des recommandations relatives à la posture de sécurité basées sur le point de référence CIS, ainsi que des recommandations propres aux appareils. Obtenez une visibilité sur la sécurité du système d’exploitation, notamment la configuration du système d’exploitation, la configuration du pare-feu et les autorisations. 

### <a name="endpoint-iotot-threat-detection"></a>Détection des menaces IoT/OT sur les points de terminaison

Détectez les menaces telles que les botnets, les tentatives par force brute, le minage de cryptomonnaie et les activités suspectes sur le réseau. Créez des alertes personnalisées pour cibler les menaces les plus importantes dans votre organisation. 

### <a name="flexible-distribution-and-deployment-models"></a>Modèles de distribution et de déploiement flexibles 

Le micro-agent Azure Defender pour IoT inclut le code source, ce qui vous permet d’incorporer le micro-agent dans le microprogramme ou de le personnaliser pour inclure uniquement ce dont vous avez besoin. Il est également disponible sous forme de package binaire ou intégré directement à d’autres solutions Azure IoT. 

## <a name="see-also"></a>Voir aussi

[Architecture de Azure Defender pour IoT](architecture.md)