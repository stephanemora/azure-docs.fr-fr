---
title: Choix d’architecture de la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article décrit les choix techniques et d’architecture effectués dans la surveillance à distance
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 27a65d8a499f6eba130dc9537de3cb2b3dc3abe8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446766"
---
# <a name="remote-monitoring-architectural-choices"></a>Choix d’architecture de la surveillance à distance

L’accélérateur de solution de supervision à distance Azure IoT est un accélérateur de solution open source sous licence MIT. Pour vous aider à accélérer votre processus de développement IoT, il présente des scénarios IoT courants, tels que :

- Connectivité des appareils
- Gestion des appareils
- Traitement des flux de données

La solution de supervision à distance suit l’[architecture de référence Azure IoT](/azure/architecture/reference-architectures/iot) recommandée.

Cet article décrit les principaux choix techniques et d’architecture effectués dans chaque sous-système de supervision à distance. Toutefois, les choix techniques effectués par Microsoft dans la solution de supervision à distance ne représentent pas la seule façon d’implémenter une solution IoT de supervision à distance. Vous devez considérer l’implémentation technique comme une base de référence pour la génération d’une application réussie et vous devez la modifier pour :

- l’adapter à l’expérience et aux compétences disponibles dans votre organisation ;
- répondre aux besoins des applications verticales.

## <a name="architectural-choices"></a>Choix d’architecture

L’architecture recommandée par Microsoft pour les applications IoT est native dans le cloud, sans serveur et basée sur les microservices. Vous devez créer les différents sous-systèmes d’une application IoT comme des services discrets que vous pouvez déployer et mettre à l’échelle de manière indépendante. Ces attributs permettent une plus grande mise à l’échelle et plus de souplesse dans la mise à jour des sous-systèmes individuels, et fournissent la flexibilité nécessaire pour choisir une technologie appropriée pour chaque sous-système.

Vous pouvez implémenter les microservices avec plusieurs technologies. Par exemple, vous pouvez choisir l’une des options suivantes pour implémenter un microservice :

- Utiliser une technologie de conteneur telle que Docker avec une technologie sans serveur comme Azure Functions.
- Héberger vos microservices dans les services PaaS comme Azure App Services.

## <a name="technology-choices"></a>Choix de technologie

Cette section détaille les choix de technologie effectués dans la solution de supervision à distance pour chaque sous-système principal.

![Diagramme principal](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Passerelle cloud

Azure IoT Hub est utilisé comme passerelle cloud de solution de supervision à distance. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) offre une communication bidirectionnelle sécurisée avec les appareils.

Pour la connectivité d’appareils IoT, vous pouvez utiliser :

- Les [kits SDK d’appareil IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) pour implémenter une application cliente native pour votre appareil. Les kits SDK proposent des wrappers autour de l’API REST IoT Hub et gèrent des scénarios tels que les nouvelles tentatives.
- L’intégration à Azure IoT Edge pour déployer et gérer les modules personnalisés qui s’exécutent dans des conteneurs sur vos appareils.
- L’intégration à la gestion automatique des appareils dans IoT Hub pour gérer des appareils connectés en bloc.

### <a name="stream-processing"></a>Traitement des flux de données

Pour le traitement des flux de données, la solution de supervision à distance utilise Azure Stream Analytics pour le traitement des règles complexes. Si vous voulez utiliser des règles plus simples, il existe un microservice personnalisé avec une prise en charge du traitement des règles simples, bien que cette configuration ne fasse pas partie du déploiement prêt à l’emploi. L’architecture de référence recommande Azure Functions pour le traitement des règles simples et Azure Stream Analytics pour le traitement des règles complexes.

### <a name="storage"></a>Stockage

En ce qui concerne le stockage, l’accélérateur de solution de supervision à distance utilise à la fois Azure Time Series Insights et Azure Cosmos DB. Azure Time Series Insights stocke les messages provenant de IoT Hub à partir de vos appareils connectés. L’accélérateur de solution utilise Azure Cosmos DB pour tous les autres systèmes de stockage tels que le stockage à froid, des définitions de règles, des alertes et des paramètres de configuration.

Azure Cosmos DB est la solution recommandée de stockage à chaud à usage général pour les applications IoT. Cependant, les solutions comme Azure Time Series Insights et Azure Data Lake sont appropriées pour nombreux cas d’utilisation. Avec Azure Time Series Insight, vous bénéficiez d’informations plus détaillées sur les données de vos capteurs Time Series et vous identifiez rapidement les tendances et les anomalies. Cette fonctionnalité vous permet d’effectuer des analyses de la cause première et d’éviter des temps d’arrêt coûteux.

> [!NOTE]
> Time Series Insights n’est pas encore disponible dans le cloud Azure Chine. Les nouveaux déploiements d’accélérateur de solution de supervision à distance dans le cloud Azure Chine utilisent Cosmos DB pour l’intégralité du stockage.

### <a name="business-integration"></a>Intégration d’entreprise

L’intégration d’entreprise dans la solution de supervision à distance est limitée à la génération des alertes, qui sont placées dans le stockage à chaud. Connectez la solution à Azure Logic Apps pour implémenter des scénarios d’intégration d’entreprise plus approfondis.

### <a name="user-interface"></a>Interface utilisateur

L’interface utilisateur web est créée avec JavaScript React. React offre un framework d’interface utilisateur web du secteur d’activité couramment utilisé et est semblable à d’autres frameworks connus comme Angular.

### <a name="runtime-and-orchestration"></a>Runtime et orchestration

La solution de supervision à distance utilise des conteneurs Docker pour exécuter les sous-systèmes avec Kubernetes comme orchestrateur pour la mise à l’échelle horizontale. Cette architecture permet une définition de mise à l’échelle individuelle pour chaque sous-système. Toutefois, elle entraîne des coûts d’opérations de développement pour que les machines virtuelles et conteneurs soient toujours à jour et sécurisés.

Les alternatives à Docker incluent l’hébergement de microservices dans les services PaaS comme Azure App Service. Les alternatives à Kubernetes incluent des orchestrateurs comme Service Fabric, DC/OS ou Swarm.

## <a name="next-steps"></a>Étapes suivantes

* Déployer votre solution de supervision à distance [ici](https://www.azureiotsolutions.com/).
* Explorer le code GitHub en [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) et [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* En savoir plus sur l’architecture de référence IoT [ici](/azure/architecture/reference-architectures/iot).