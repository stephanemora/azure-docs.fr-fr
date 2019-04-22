---
title: Prise en main Azure Security Center (ASC) pour la version préliminaire IoT | Microsoft Docs
description: Commencez à comprendre le workflow de base d’Azure Security Center pour les fonctionnalités de IoT et de service.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: aac15d766439a725f593ca421cbdc6da496f29f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862690"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Prise en main Azure Security Center pour IoT 

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article fournit une explication des différents blocs de construction de Azure Security Center (ASC) pour le service IoT et explique comment bien démarrer avec [l’activation du service](quickstart-onboard-iot-hub.md). 

ASC pour IoT peut être intégrée en toute transparence dans votre IoT Hub pour fournir une analyse de sécurité de configuration du hub IoT, identité d’appareil et de hub-appareil modèles de communication.
Pour les capacités améliorées de sécurité, ASC pour IoT fournit basés sur l’agent de collecte de données de sécurité de vos appareils IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC pour une intégration transparente IoT Hub IoT

Lorsque vous tentez de protéger vos appareils IoT individuels, la possibilité de collecter des données directement à partir des appareils, ou à partir de leur réseau est requise. Pour prendre en charge cet effort, ASC pour IoT offre un arsenal d’agents de sécurité de faible encombrement permettant l’analyse des périphériques et renforcement.

Dans ASC pour IoT preview, l’architecture de référence pour les agents de sécurité Linux et Windows, à la fois dans C# et C sont fournies.
Les agents de gérer la collecte d’événements brutes à partir du système d’exploitation, l’agrégation d’événements pour réduire les coûts et configuration via un jumeau de module.
Sécurité sont envoyés via votre IoT Hub dans ASC pour les services d’analytique IoT.

## <a name="asc-for-iot-basics"></a>ASC concepts de base IoT

Choisissez le scénario de flux de travail qui répond le mieux à vos besoins d’appareils et les environnements IoT :

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Prise en main ASC pour une intégration transparente IoT Hub IoT 

>[!Note]
>Ce flux de travail vous permet d’utiliser le service sans utiliser ASC pour les agents de sécurité IoT. 

Pour activer la surveillance de votre appareil gestion des identités, l’appareil vers cloud et cloud pour les modèles de communication de périphérique, utilisez suivant du flux de travail pour le test et pour démarrer le service : 

1. [Activer ASC pour le service IoT sur votre IoT Hub](quickstart-onboard-iot-hub.md)
1. Si votre IoT Hub ne dispose d’aucun appareil inscrit, [enregistrez un nouvel appareil](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Créer un module de sécurité azureiotsecurity pour vos appareils](quickstart-create-security-twin.md) pour vos appareils. 
1. Définir le comportement normal de l’appareil et système via [alertes personnalisées](quickstart-create-custom-alerts.md). 
1. Effectuer un test pour vérifier l’état de service et le périphérique du système. 
1. Explorer [alertes](concept-security-alerts.md), [recommandations](concept-recommendations.md), et [présentation approfondie à l’aide d’Analytique de journal](how-to-security-data-access.md) à l’aide de IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Prise en main ASC pour les agents de sécurité IoT

Utilisons ASC pour les fonctionnalités de sécurité IoT améliorée, comme la surveillance des connexions à distance, les applications actives, les événements de connexion et les meilleures pratiques de configuration du système d’exploitation à l’aide du flux de travail suivant pour tester et activer le service : 

1. [Activer ASC pour le service IoT à votre IoT Hub](quickstart-onboard-iot-hub.md)
1. Si votre IoT Hub ne dispose d’aucun appareil inscrit, [enregistrez un nouvel appareil](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Créer un module de sécurité azureiotsecurity](quickstart-create-security-twin.md) pour vos appareils.
1. Pour installer l’agent sur un appareil simulé Azure au lieu d’installer sur un appareil réel, [toupie (spin) d’une nouvelle Machine virtuelle (machine virtuelle Azure)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) dans une zone disponible. 
1. [Déployer un ASC pour l’agent de sécurité IoT](how-to-deploy-linux-cs.md) sur votre appareil IoT ou une nouvelle machine virtuelle.
1. Suivez les instructions pour [trigger_events](https://aka.ms/iot-security-github-trigger-events) pour exécuter une simulation d’une attaque inoffensif.
1. Vérifiez ASC pour les alertes IoT en réponse à l’attaque simulé à l’étape précédente. Commencer la vérification cinq minutes après avoir exécuté le script.
1. Explorer [alertes](concept-security-alerts.md), [recommandations](concept-recommendations.md), et [présentation approfondie à l’aide d’Analytique de journal](how-to-security-data-access.md) à l’aide de IoT Hub. 

## <a name="next-steps"></a>Étapes suivantes

- Activer [ASC pour IoT](quickstart-onboard-iot-hub.md)
- Configurer votre [solution](quickstart-configure-your-solution.md)
- [Créer des modules de sécurité](quickstart-create-security-twin.md)
- Configurer [alertes personnalisées](quickstart-create-custom-alerts.md)
- [Déployer un agent de sécurité](how-to-deploy-agent.md)
