---
title: Azure Defender pour IoT
description: En savoir plus sur Azure Defender pour IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448338"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Présentation d’Azure Defender pour IoT

Unifiez les fonctionnalités de gestion de la sécurité et activez l’analyse et la détection contre les menaces de bout en bout dans l’ensemble des charges de travail cloud hybrides et votre solution Azure IoT.

Azure Defender pour IoT offre les fonctionnalités suivantes :

- **Découverte des ressources et mappage réseau**, y compris les informations telles que le fabricant de l’appareil, le type d’appareil et la façon dont les appareils communiquent sur le réseau
- **Gestion des vulnérabilités**, y compris les informations sur les CVE, les ports ouverts et les connexions Internet non autorisées
- **Surveillance continue des menaces**, avec des alertes en temps réel indiquant toute activité anormale ou non autorisée comme les attaques ciblées ou les logiciels malveillants

Pour des détails complets, consultez [la documentation dédiée](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Disponibilité
|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|Nécessite [Azure Defender](security-center-pricing.md)|
|Rôles et autorisations obligatoires :|Autorisations en écriture sur les groupes de sécurité de la machine|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) National/souverain (US Gov, Chine Gov, autres Gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Quels appareils Azure Defender pour IoT peut-il sécuriser ?
En combinant les fonctionnalités de Security Center, Azure Defender et la technologie sans agent CyberX, vous pouvez sécuriser :

- Les **appareils Greenfield** connectés via IoT Hub. Cela permet aux organisations d’accélérer leurs initiatives IoT en sécurisant les appareils modernes et Internet natifs, ainsi que les appareils ICS/SCADA traditionnels grâce à une solution unifiée unique.
    - Intégrez les nouveaux appareils, et appliquez des stratégies de sécurité à l’ensemble de vos charges de travail (appareils de nœud, appareils Microsoft Edge, IoT Hub) pour garantir la conformité avec les standards de sécurité et une posture de sécurité améliorée.

- **Appareils « brownfield » non gérés** utilisés dans les environnements de formation opérationnelle, tels que la fabrication, les systèmes de gestion de bâtiments (BMS), les sciences de la vie, les distributeurs d’eau et d’énergie, le pétrole et le gaz, la logistique. 
    - Pour protéger les appareils non gérés, Azure Defender pour IoT utilise un capteur local pour effectuer une analyse du trafic réseau (NTA) passive et non invasive, qui n’a aucun impact sur les performances des environnements OT. 
    - Il intègre également une compréhension approfondie des protocoles IoT/OT spécialisés, avec une analyse brevetée du comportement et une solution Machine Learning compatibles IoT/OT, afin d’éviter d’avoir à configurer des règles ou des signatures, ce qui entraîne des déploiements classiques en quelques minutes ou quelques heures, et non plus en plusieurs jours ou semaines. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Intégration d’Azure Defender pour IoT avec Azure Sentinel
Pour permettre une surveillance et une gouvernance de sécurité IT/OT unifiées, Azure Defender pour IoT s’intègre en mode natif avec [Azure Sentinel](../sentinel/overview.md).

Les équipes responsables des opérations de sécurité peuvent ainsi :

- Détecter et répondre rapidement aux menaces IoT/OT par le biais de playbooks SOAR (Security Orchestration Automated Response) spécifiques à OT, inclus dans Sentinel
- Tirer parti de la mise à jour en continu des informations sur les menaces spécifiques à IoT/OT fournies par Section 52, l’équipe interne de Microsoft chargée de la surveillance des menaces
- Intégrer Azure Defender pour IoT à des flux de travail SOC existants et à des outils de sécurité tiers comme Splunk, IBM QRadar et ServiceNow


## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté Azure Defender pour IoT dans Azure Security Center. Pour plus d'informations, consultez les pages suivantes :

- [Présentation d’Azure Security Center pour IoT](../asc-for-iot/overview.md)
