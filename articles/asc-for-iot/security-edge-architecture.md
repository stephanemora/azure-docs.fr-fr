---
title: Comprendre le module de sécurité Azure Security Center pour l’IoT pour IoT Edge | Microsoft Docs
description: Découvrez l’architecture et les fonctionnalités du module de sécurité Azure Security Center pour l’IoT pour IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 847d6238bd42dc7da723dcc9acd47ed09c16dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200577"
---
# <a name="azure-iot-edge-security-module"></a>Module de sécurité Azure IoT Edge

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion, proposée sans contrat de niveau de service, n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) offre de puissantes fonctionnalités de gestion et d’exécution de flux de travail à la périphérie.
Le rôle clé d’IoT Edge dans les environnements IoT le rend particulièrement séduisant pour les personnes malveillantes.

Le module de sécurité Azure Security Center (ASC) pour l’IoT constitue une solution de sécurité complète pour les appareils IoT Edge.
Il collecte, agrège et analyse des données de sécurité brutes tirées du système d’exploitation et du système de conteneur pour produire des alertes et des recommandations de sécurité actionnables.

Tout comme les agents de sécurité ASC pour les appareils IoT, le module ASC pour IoT Edge est hautement personnalisable grâce à son jumeau de module.
Pour plus d’informations, voir [Configurer un agent](how-to-agent-configuration.md).

Le module de sécurité ASC pour l’IoT pour IoT Edge offre les fonctionnalités suivantes :

- Collecte d’événements de sécurité bruts auprès du système d’exploitation sous-jacent (Linux) et des systèmes de conteneurs IoT Edge.
  
  Pour plus d’informations sur les collecteurs de données de sécurité disponibles, voir [Configuration de l’agent ASC pour l’IoT](how-to-agent-configuration.md).

- Analyse des manifestes de déploiement IoT Edge.

- Agrégation des événements de sécurité bruts dans des messages envoyés par un [hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Configuration à distance grâce au jumeau de module de sécurité.

  Pour plus d’informations, voir [Configurer un agent ASC pour l’IoT](how-to-agent-configuration.md).

Le module de sécurité ASC pour l’IoT pour IoT Edge s’exécute en mode privilégié sous IoT Edge.
Ce mode est nécessaire pour l’autoriser à effectuer un monitoring du système d’exploitation et d’autres modules IoT Edge.

## <a name="agent-supported-platforms"></a>Plateformes prises en charge par l’agent

Le module de sécurité ASC pour l’IoT pour IoT Edge n’est actuellement disponible que pour Linux.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture et les fonctionnalités du module de sécurité ASC pour l’IoT pour IoT Edge.

Pour continuer votre prise en main du déploiement d’ASC pour l’IoT, lisez les articles suivants :

- Déployez le [module de sécurité pour IoT Edge](how-to-deploy-edge.md)
- Découvrez comment [configurer votre module de sécurité](how-to-agent-configuration.md)
- Consultez les [Prérequis du service](service-prerequisites.md) ASC pour l’IoT
- Découvrez comment [Activer le service ASC pour l’IoT dans votre hub IoT](quickstart-onboard-iot-hub.md)
- Renseignez-vous sur le service dans la [FAQ ASC pour l’IoT](resources-frequently-asked-questions.md)