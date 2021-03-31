---
title: Micro-agent Defender-IoT de Defender pour IoT pour IoT Edge
description: Découvrez l’architecture et les capacités du micro-agent Defender-IoT d’Azure Defender pour IoT pour IoT Edge.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 81eb8816e1bcf74a9e27e34d14465102599c7d5d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782654"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Micro-agent Defender-IoT d’Azure Defender pour IoT Edge

[Azure IoT Edge](../iot-edge/index.yml) offre de puissantes fonctionnalités de gestion et d’exécution de flux de travail à la périphérie.
Le rôle clé d’IoT Edge dans les environnements IoT le rend particulièrement séduisant pour les personnes malveillantes.

Le micro-agent Defender-IoT de Defender pour IoT constitue une solution de sécurité complète pour vos appareils IoT Edge.
Il collecte, agrège et analyse des données de sécurité brutes tirées du système d’exploitation et du système de conteneur pour produire des alertes et des suggestions de sécurité actionnables.

Tout comme les agents de sécurité Defender pour IoT destinés aux appareils IoT, le module Defender pour IoT Edge est hautement personnalisable grâce à son jumeau de module.
Pour plus d’informations, voir [Configurer un agent](how-to-agent-configuration.md).

Le micro-agent Defender-IoT de Defender pour IoT pour IoT Edge offre les fonctionnalités suivantes :

- Collecte d’événements de sécurité bruts auprès du système d’exploitation sous-jacent (Linux) et des systèmes de conteneurs IoT Edge.

  Pour en savoir plus sur les collecteurs de données de sécurité disponibles, consultez [Configuration de l'agent Defender pour IoT](how-to-agent-configuration.md).

- Analyse des manifestes de déploiement IoT Edge.

- Agrégation des événements de sécurité bruts dans des messages envoyés par un [hub IoT Edge](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Suppression de la configuration à l’aide du jumeau du micro-agent Defender-IoT.

  Pour plus d'informations, consultez [Configurer un agent Defender pour IoT](how-to-agent-configuration.md).

Le micro-agent Defender-IoT de Defender pour IoT pour IoT Edge s’exécute en mode privilégié sous IoT Edge.
Ce mode est nécessaire pour l’autoriser à effectuer un monitoring du système d’exploitation et d’autres modules IoT Edge.

## <a name="module-supported-platforms"></a>Plateformes prises en charge du module

Le micro-agent Defender-IoT de Defender pour IoT pour IoT Edge n’est actuellement disponible que pour Linux.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture et les capacités du micro-agent Defender-IoT de Defender pour IoT pour IoT Edge.

Pour poursuivre votre prise en main du déploiement de Defender pour IoT, lisez les articles suivants :

- Déployer le [micro-agent Defender-IoT pour IoT Edge](how-to-deploy-edge.md)
- Découvrez comment [configurer votre micro-agent Defender-IoT](how-to-agent-configuration.md)
- Évaluer Defender pour IoT [Defender pour IoT Horizon](resources-manage-proprietary-protocols.md)
- Apprenez à [activer le service Defender pour IoT dans votre hub IoT](quickstart-onboard-iot-hub.md)
- En savoir plus sur le service dans la [FAQ Defender pour IoT](resources-frequently-asked-questions.md)