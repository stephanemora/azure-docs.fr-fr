---
title: Vue d’ensemble des agents de sécurité
description: Familiarisez-vous avec l'architecture des agents de sécurité utilisés dans le service Azure Defender pour IoT.
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
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: ff837fe88f878c522366b2b6bc19a1ef3954b667
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820651"
---
# <a name="security-agent-reference-architecture"></a>Architecture de référence de l’agent de sécurité

Azure Defender pour IoT fournit une architecture de référence pour les agents de sécurité qui consignent, traitent, agrègent et envoient des données de sécurité via IoT Hub.

Les agents de sécurité sont conçus pour fonctionner dans un environnement IoT restreint et sont hautement personnalisables quant aux valeurs qu’ils fournissent par rapport aux ressources qu’ils utilisent.

Les agents de sécurité prennent en charge les fonctionnalités suivantes :

- Authentification avec une identité d’appareil existante, ou une identité de module dédiée. Pour en savoir plus, consultez  [Méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md).

- Collecte d’événements de sécurité bruts à partir du système d’exploitation sous-jacent (Linux, Windows). Pour en savoir plus sur les collecteurs de données de sécurité disponibles, consultez [Configuration de l'agent Defender pour IoT](how-to-agent-configuration.md).

- Regroupez des événements de sécurité bruts dans des messages envoyés via IoT Hub.

- Configuration à distance à l’aide du jumeau de module **azureiotsecurity**. Pour en savoir plus, consultez [Configurer un agent Defender pour IoT](how-to-agent-configuration.md).

Les agents de sécurité Defender pour IoT sont développés en tant que projets open source et sont disponibles sur GitHub :

- [Agent Defender pour IoT basé sur C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Agent Defender pour IoT basé sur C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plateformes prises en charge par l’agent

Defender pour IoT propose différents agents d'installation pour les versions 32 et 64 bits de Windows, ainsi que pour les versions 32 et 64 bits de Linux. Vérifiez que vous avez le programme d’installation d’agent approprié pour chacun de vos appareils conformément au tableau suivant :

| Architecture | Linux | Windows | Détails |
|--|--|--|--|
| 32 bit | C | C# |  |
| 64 bits | C# ou C | C# | Nous vous recommandons d’utiliser l’agent C pour les appareils présentant un nombre de ressources d’appareils minimales ou restreintes. |


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu une vue d’ensemble de haut niveau de l'architecture du module de sécurité Defender pour IoT et des programmes d'installation disponibles.

Pour poursuivre votre prise en main du déploiement de Defender pour IoT, lisez les articles suivants :

- Compréhension des [méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)
- Sélectionner et déployer un [agent de sécurité](how-to-deploy-agent.md)
- Consultez les [Prérequis système](quickstart-system-prerequisites.md) Defender pour IoT
- Apprenez à [activer le service Defender pour IoT dans votre hub IoT](quickstart-onboard-iot-hub.md)
- Renseignez-vous sur le service dans la [FAQ Defender pour IoT](resources-frequently-asked-questions.md)
