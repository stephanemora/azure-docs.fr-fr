---
title: Bien démarrer avec les agents de sécurité Azure Security Center pour IoT | Microsoft Docs
description: Prenez un bon départ avec la compréhension, la configuration, le déploiement et l’utilisation des agents de sécurité Azure Security Center pour IoT sur vos appareils IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664182"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Bien démarrer avec les agents de sécurité d’appareils Azure Security Center pour IoT

Les agents de sécurité Azure Security Center pour IoT offrent des capacités de sécurité améliorées, telles que la surveillance de connexions distantes, les applications actives, les événements de connexion, et les bonnes pratiques en matière de configuration du système d'exploitation. Prenez le contrôle de la protection de l’appareil contre les menaces et de la posture de sécurité avec un seul service. 

L’architecture de référence des agents de sécurité Linux et Windows est fournie à la fois en C# et en C.

Les agents de sécurité Azure Security Center pour IoT gèrent la collecte d’événements bruts auprès du système d’exploitation, l’agrégation d’événements pour réduire les coûts et la configuration grâce à un jumeau de module. Les messages de sécurité sont envoyés aux services d’analyse d’Azure Security Center pour IoT par le hub IoT.

Utilisez le flux de travail suivant pour déployer et tester vos agents de sécurité Azure Security Center pour IoT : 

1. [Activer le service Azure Security Center pour IoT sur votre IoT Hub](quickstart-onboard-iot-hub.md)
1. Si aucun appareil n’est inscrit sur votre hub IoT, [Enregistrez un nouvel appareil](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Créez un module de sécurité azureiotsecurity](quickstart-create-security-twin.md) pour vos appareils.
1. Pour installer l’agent sur un appareil simulé Azure au lieu d’un appareil réel, [lancez une nouvelle Machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) dans une zone disponible. 
1. [Déployez un l’agent de sécurité Azure Security Center pour IoT](how-to-deploy-linux-cs.md) sur votre appareil IoT ou une nouvelle machine virtuelle.
1. Suivez les instructions de [trigger_events](https://aka.ms/iot-security-github-trigger-events) pour exécuter une simulation d’attaque sans danger.
1. Vérifiez les alertes Azure Security Center pour IoT qui surviennent en réponse à l’attaque simulée à l’étape précédente. Commencez la vérification cinq minutes après avoir exécuté le script.
1. Explorez les [alertes](concept-security-alerts.md), les [recommandations](concept-recommendations.md) et la [présentation approfondie de Log Analytics](how-to-security-data-access.md) avec IoT Hub. 


## <a name="next-steps"></a>Étapes suivantes

- Configurer une [solution](quickstart-configure-your-solution.md)
- [Créer des modules de sécurité](quickstart-create-security-twin.md)
- Configurer des [alertes personnalisées](quickstart-create-custom-alerts.md)
- [Déployer un agent de sécurité](how-to-deploy-agent.md)
