---
title: Vue d’ensemble du module de sécurité pour Azure RTOS
description: Apprenez-en davantage sur le module de sécurité pour la prise en charge et l’implémentation d’Azure RTOS dans le cadre du service Azure Security Center pour IoT.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514473"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>Présentation : Module de sécurité pour Azure RTOS (préversion)

Le module de sécurité Azure Security Center pour IoT RTOS constitue une solution de sécurité complète pour les appareils Azure RTOS. Azure RTOS est désormais fourni avec le module de sécurité Azure IoT intégré et fournit une couverture contre les menaces courantes et les activités potentiellement malveillantes sur le système d’exploitation des appareils en temps réel. 

![Azure RTOS d’Azure Security Center pour IoT](./media/architecture/azure-rtos-security-monitoring.png)


Le module de sécurité pour Azure RTOS offre les fonctionnalités suivantes : 
- Détection des activités réseau malveillantes
- Établissement d’une ligne de base du comportement de l’appareil basé sur des alertes personnalisées
- Amélioration de l’hygiène de sécurité des appareils

### <a name="detection-of-malicious-network-activities"></a>Détection des activités malveillantes sur les réseaux

L’activité réseau entrante et sortante de chaque appareil est analysée (protocoles pris en charge : TCP, UDP, ICMP sur IPv4 et IPv6). Azure Security Center pour IoT inspecte chacune de ces activités réseau par rapport au flux de renseignements sur les menaces de Microsoft. Le flux est mis à jour en temps réel par des millions d’indicateurs de menace uniques collectés dans le monde entier. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Établissement d’une ligne de base du comportement de l’appareil basé sur des alertes personnalisées

L’établissement d’une ligne de base permet le clustering d’appareils dans des groupes de sécurité et la définition du comportement attendu de chaque groupe. Étant donné que les appareils IoT sont généralement conçus pour fonctionner dans des scénarios limités et bien définis, il est facile de créer une ligne de base qui définit le comportement attendu à l’aide d’un ensemble de paramètres. Tout écart par rapport à la ligne de base déclenche une alerte. 

### <a name="improve-your-device-security-hygiene"></a>Amélioration de l’hygiène de sécurité de vos appareils

En tirant parti de l’infrastructure recommandée par Azure Security Center pour IoT, acquérez des connaissances et des informations sur les problèmes de votre environnement qui ont un impact sur la sécurité de vos appareils. Une posture de sécurité faible des appareils IoT peut permettre à des attaques potentielles d’aboutir si elle reste inchangée, car la sécurité est toujours mesurée selon le maillon le plus faible au sein d’une organisation. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Prise en main de la protection des appareils Azure RTOS

Le module de sécurité pour Azure RTOS est fourni en téléchargement gratuit pour vos appareils. Le service cloud Azure Security Center pour IoT est disponible avec un essai gratuit de 30 jours par abonnement Azure. Pour commencer, téléchargez le [module de sécurité pour Azure RTOS](https://github.com/azure-rtos/iot-security-module-preview). 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert le module de sécurité pour le service Azure RTOS. Pour en savoir plus sur le module de sécurité et le prendre en main, consultez les articles suivants :

- [Concepts relatifs au module de sécurité IoT pour Azure RTOS](concept-rtos-security-module.md)
- [Démarrage rapide : Module de sécurité IoT pour Azure RTOS](quickstart-azure-rtos-security-module.md)


