---
title: Agents de sécurité
description: Prenez un bon départ avec la compréhension, la configuration, le déploiement et l’utilisation des agents du service de sécurité Azure Defender pour IoT sur vos appareils IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: a40b64dd3b8f898c961863c1d78a2a56642c44b9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521609"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Bien démarrer avec les micro-agents d’appareils Azure Defender pour IoT

Les agents de sécurité Defender pour IoT offrent des fonctionnalités de sécurité améliorées, telles que la surveillance des meilleures pratiques en matière de configuration du système d’exploitation. Prenez le contrôle de la protection de l’appareil contre les menaces et de la posture de sécurité avec un seul service.

Les agents de sécurité Defender pour IoT gèrent la collecte d’événements bruts auprès du système d’exploitation, l’agrégation d’événements pour réduire les coûts et la configuration grâce à un jumeau de module. Les messages de sécurité sont envoyés aux services d’analyse de Defender pour l’IoT par le hub IoT.

Utilisez le flux de travail suivant pour déployer et tester vos agents de sécurité Defender pour IoT :

1. [Activez le service Defender pour IoT sur votre IoT Hub](quickstart-onboard-iot-hub.md).

1. Si aucun appareil n’est inscrit sur votre hub IoT, [Enregistrez un nouvel appareil](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Créez un jumeau de module DefenderIotMicroAgent](quickstart-create-micro-agent-module-twin.md) pour vos appareils.

1. Pour installer l’agent sur un appareil simulé Azure au lieu d’un appareil réel, [lancez une nouvelle Machine virtuelle Azure](../virtual-machines/linux/quick-create-portal.md) dans une zone disponible.

1. [Déployez un l’agent de sécurité Defender pour IoT](how-to-deploy-linux-cs.md) sur votre appareil IoT ou une nouvelle machine virtuelle.

1. Suivez les instructions pour [trigger_events](https://aka.ms/iot-security-github-trigger-events) afin d’exécuter un événement de base de système d’exploitation.

1. Vérifiez les recommandations de Defender pour IoT en réponse à l’échec de vérification de base du système d’exploitation simulé à l’étape précédente. Commencez la vérification 30 minutes après l’exécution du script.

## <a name="next-steps"></a>Étapes suivantes

Configurer votre [solution](quickstart-configure-your-solution.md)
[Créer des modules de sécurité](quickstart-create-security-twin.md) Configurer des [alertes personnalisées](quickstart-create-custom-alerts.md)
[Déployer un agent de sécurité](how-to-deploy-agent.md)
