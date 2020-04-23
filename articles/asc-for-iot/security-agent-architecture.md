---
title: Architecture de l’agent de sécurité
description: Comprenez l’architecture de l’agent de sécurité pour les agents utilisés dans Azure Security Center pour le service IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310685"
---
# <a name="security-agent-reference-architecture"></a>Architecture de référence de l’agent de sécurité

Azure Security Center pour IoT fournit une architecture de référence pour les agents de sécurité qui se connectent, traitent, rassemblent et envoient des données de sécurité via le IoT Hub.

Les agents de sécurité sont conçus pour fonctionner dans un environnement IoT restreint et sont hautement personnalisables quant aux valeurs qu’ils fournissent par rapport aux ressources qu’ils utilisent.

Les agents de sécurité prennent en charge les fonctionnalités suivantes :

- Collecte d’événements de sécurité bruts à partir du système d’exploitation sous-jacent (Linux, Windows). Pour en savoir plus sur les collecteurs de données de sécurité disponibles, voir [Didacticiel : Configurer des agents de sécurité](how-to-agent-configuration.md).

- Regroupez des événements de sécurité bruts dans des messages envoyés via IoT Hub.

- Authentification avec une identité d’appareil existante, ou une identité de module dédiée. Consultez [Méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md) pour en savoir plus.

- Configuration à distance à l’aide du jumeau de module **azureiotsecurity**. Pour en savoir plus, voir [Didacticiel : Configurer des agents de sécurité](how-to-agent-configuration.md).

Les agents de sécurité Azure Security Center pour IoT sont développés en tant que projets open source et sont disponibles sur GitHub :

- [Agent Azure Security Center pour IoT basé sur C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Agent Azure Security Center pour IoT basé sur C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plateformes prises en charge par l’agent

Azure Security Center pour IoT offre des agents de programme d’installation différents pour les versions 32 et 64 bits de Windows, et les mêmes pour les versions 32 et 64 bits de Linux. Vérifiez que vous avez le programme d’installation d’agent approprié pour chacun de vos appareils conformément au tableau suivant :

| Architecture | Linux | Windows |    Détails|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bits  | C  | C#  ||
| 64 bits  | C# ou C           | C#      | Nous vous recommandons d’utiliser l’agent C pour les appareils présentant un nombre de ressources d’appareils minimales ou restreintes.|
|

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture de l’agent de sécurité Azure Security Center pour IoT, ainsi que les programmes d’installation disponibles.

Pour continuer votre prise en main du déploiement d’Azure Security Center pour IoT, lisez les articles suivants :

- Compréhension des [méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)
- Sélectionner et déployer un [agent de sécurité](how-to-deploy-agent.md)
- Révisez les [composants requis pour le service](service-prerequisites.md) Azure Security Center pour IoT
- Découvrez comment [activer le service Azure Security Center pour IoT dans votre IoT Hub](quickstart-onboard-iot-hub.md)
- Trouvez des informations sur le service dans la [FAQ Azure Security Center pour IoT](resources-frequently-asked-questions.md)
