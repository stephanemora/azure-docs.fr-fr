---
title: Choix d’architecture de la solution de surveillance à distance - Azure | Microsoft Docs
description: Cet article décrit les choix techniques et d’architecture effectués dans la surveillance à distance
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 192a763c01e60d816ae2046587176627fc9d8736
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="remote-monitoring-architectural-choices"></a>Choix d’architecture de la surveillance à distance

La surveillance à distance Azure IoT est un accélérateur de solution open source sous licence MIT qui présente des scénarios IoT courants tels que la connectivité des appareils, la gestion des appareils et le traitement des flux de données, afin que les clients puissent accélérer leurs processus de développement.  La surveillance à distance suit l’architecture de référence Azure IoT recommandée publiée [ici](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

Cet article décrit les choix techniques et d’architecture effectués dans chaque sous-système pour la surveillance à distance, et présente les autres solutions envisagées.  Il est important de noter que les choix techniques effectués dans la surveillance à distance ne représentent pas la seule façon d’implémenter une solution IoT de surveillance à distance.  L’implémentation technique est une base de référence pour la génération d’une application réussie et doit être modifiée pour s’adapter aux compétences, à l’expérience et aux besoins des applications verticales pour l’implémentation d’une solution cliente.

## <a name="architectural-choices"></a>Choix d’architecture

### <a name="microservices-serverless-and-cloud-native"></a>Microservices, sans serveur et native dans le cloud

L’architecture recommandée pour les applications IoT est native dans le cloud, sans serveur et basée sur les microservices.  Les différents sous-systèmes d’une application IoT doivent être créés comme des services discrets déployables et pouvant être mis à l’échelle de manière indépendante.  Ces attributs permettent une plus grande mise à l’échelle et plus de souplesse dans la mise à jour des sous-systèmes individuels, et fournissent la flexibilité nécessaire pour choisir la technologie appropriée selon le sous-système.  Les microservices peuvent être implémentés avec plusieurs technologies. Citons à titre d’exemple l’utilisation d’une technologie de conteneur telle que Docker avec une technologie sans serveur comme Azure Functions, ou l’hébergement de microservices dans les services PaaS comme Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Choix de technologie des principaux sous-systèmes

Cette section détaille les choix de technologie effectués dans la surveillance à distance pour chaque sous-système principal.

![Diagramme principal](media/iot-suite-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Passerelle cloud
Le service Azure IoT Hub est utilisé comme passerelle cloud de surveillance à distance.  Le hub IoT offre une communication bidirectionnelle sécurisée avec les appareils. Vous en apprendrez davantage sur IoT Hub [ici](https://azure.microsoft.com/services/iot-hub/). Pour la connectivité d’appareils IoT, les kits SDK .NET Core et Java IoT Hub sont utilisés.  Les kits SDK proposent des wrappers autour de l’API REST IoT Hub et gèrent des scénarios tels que les nouvelles tentatives. 

### <a name="stream-processing"></a>Traitement des flux de données
Pour le traitement des flux de données, la surveillance à distance utilise Azure Stream Analytics pour le traitement des règles complexes.  Pour les clients qui souhaitent des règles plus simples, nous avons également un microservice personnalisé avec une prise en charge du traitement des règles simples, bien que cette configuration ne fasse pas partie du déploiement prêt à l’emploi. L’architecture de référence recommande d’utiliser Azure Functions pour le traitement des règles simples et Azure Stream Analytics pour le traitement des règles complexes.  

### <a name="storage"></a>Stockage
Pour le stockage, Cosmos DB est utilisé pour tous les besoins de stockage : stockage à froid, stockage à chaud, stockage des règles et alarmes. Nous sommes actuellement en train de passer au stockage Blob Azure, comme recommandé par l’architecture de référence.  Cosmos DB est la solution recommandée pour le stockage à chaud à usage général pour les applications IoT même si des solutions comme Azure Time Series Insights et Azure Data Lake sont appropriées à de nombreux cas d’utilisation.

### <a name="business-integration"></a>Intégration d’entreprise
L’intégration d’entreprise dans la surveillance à distance est limitée à la génération des alarmes, qui sont placées dans le stockage à chaud. D’autres intégrations d’entreprise peuvent être effectuées en intégrant la solution à Azure Logic Apps.

### <a name="user-interface"></a>Interface utilisateur
L’interface utilisateur web est créée avec JavaScript React.  React offre un framework d’interface utilisateur web du secteur d’activité couramment utilisé et est semblable à d’autres frameworks connus comme Angular.  

### <a name="runtime-and-orchestration"></a>Runtime et orchestration
Le runtime d’application choisi pour l’implémentation d’un sous-système dans la surveillance à distance correspond aux conteneurs Docker avec Kubernetes (K8s) comme orchestrateur pour la mise à l’échelle horizontale.  Cette architecture permet une définition de mise à l’échelle individuelle par sous-système, mais entraîne des coûts d’opérations de développement pour que les machines virtuelles et conteneurs soient toujours à jour du point de vue de la sécurité.  Les alternatives à Docker et K8s incluent l’hébergement de microservices dans les services PaaS (par exemple, Azure App Service) ou l’utilisation de Service Fabric, DCOS, Swarm, etc. comme orchestrateur.

## <a name="next-steps"></a>Étapes suivantes
* Déployer votre solution de surveillance à distance [ici](https://www.azureiotsuite.com/).
* Explorer le code GitHub en [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) et [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* En savoir plus sur l’architecture de référence IoT [ici](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
