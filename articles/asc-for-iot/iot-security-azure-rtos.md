---
title: Prise en charge d’Azure RTOS
description: En savoir plus sur la prise en charge d’Azure RTOS dans le service Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094103"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Solution de sécurité Azure Security Center pour IoT pour Azure RTOS 

Le module de sécurité Azure Security Center pour IoT constitue une solution de sécurité complète pour les appareils Azure RTOS. Azure RTOS est équipé d’un module de sécurité intégré qui couvre les menaces courantes sur les systèmes d’exploitation en temps réel. 

![Azure RTOS d’Azure Security Center pour IoT](./media/architecture/azure-rtos-security-monitoring.png)


Le module de sécurité Azure Security Center pour IoT avec prise en charge d’Azure RTOS offre les fonctionnalités suivantes : 
- Détection des activités réseau malveillantes
- Établissement d’une ligne de base du comportement de l’appareil basé sur des alertes personnalisées
- Amélioration de l’hygiène de sécurité des appareils

### <a name="detection-of-malicious-network-activities"></a>Détection des activités malveillantes sur les réseaux

L’activité réseau entrante et sortante de chaque appareil est analysée (protocoles pris en charge : TCP, UDP, ICMP sur IPv4 et IPv6). Azure Security Center pour IoT inspecte chacune de ces activités réseau par rapport au flux de renseignements sur les menaces de Microsoft. Le flux est mis à jour en temps réel par des millions d’indicateurs de menace uniques collectés dans le monde entier. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Établissement d’une ligne de base du comportement de l’appareil basé sur des alertes personnalisées

L’établissement d’une ligne de base permet le clustering d’appareils dans des groupes de sécurité et la définition du comportement attendu de chaque groupe. Étant donné que les appareils IoT sont généralement conçus pour fonctionner dans des scénarios limités et bien définis, il est facile de créer une ligne de base qui définit le comportement attendu à l’aide d’un ensemble de paramètres. Tout écart par rapport à la ligne de base déclenche une alerte. 

### <a name="improve-your-device-security-hygiene"></a>Amélioration de l’hygiène de sécurité de vos appareils

En tirant parti de l’infrastructure recommandée par Azure Security Center pour IoT, acquérez des connaissances et des informations sur les problèmes de votre environnement qui ont un impact sur la sécurité de vos appareils. Une mauvaise posture de sécurité des appareils IoT peut permettre à des attaques potentielles de réussir si elle reste inchangée, car la sécurité est toujours mesurée selon le maillon le plus faible au sein d’une organisation. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Prise en main de la protection des appareils Azure RTOS

- Le module de sécurité Azure Security Center pour IoT pour Azure RTOS est fourni en téléchargement gratuit pour vos appareils. Le service cloud Azure Security Center pour IoT est disponible avec un essai gratuit de 30 jours par abonnement Azure. Téléchargez le [module de sécurité Azure Security Center pour IoT pour Azure RTOS](https://github.com/azure-rtos/iot-security-module-preview) pour commencer. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert la prise en charge d’Azure RTOS d’Azure Security Center pour IoT. Pour en savoir plus sur la prise en main et l’activation de votre solution de sécurité dans IoT Hub, consultez les articles suivants :

- [Prérequis du service](service-prerequisites.md)
- [Bien démarrer](getting-started.md)
- [Configurer votre solution](quickstart-configure-your-solution.md)
- [Activer la sécurité dans IoT Hub](quickstart-onboard-iot-hub.md)
- [FAQ Azure Security Center pour IoT](resources-frequently-asked-questions.md)
- [Alertes de sécurité Azure Security Center pour IoT](concept-security-alerts.md)
