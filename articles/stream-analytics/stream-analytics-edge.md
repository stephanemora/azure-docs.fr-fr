---
title: Azure Stream Analytics sur IoT Edge
description: Créez des tâches de périphérie dans Azure Stream Analytics et déployez-les sur des appareils exécutant Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012612"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics sur IoT Edge
 
Azure Stream Analytics sur IoT Edge encourage les développeurs à déployer une intelligence analytique quasiment en temps réel plus proche des appareils IoT pour leur permettre de déverrouiller la valeur complète des données générées par l’appareil. Azure Stream Analytics est conçu pour une latence faible, une résilience, une utilisation efficace de la bande passante et la conformité. Les entreprises peuvent déployer la logique de contrôle proche des opérations industrielles et compléter l’analytique du Big Data effectuée dans le cloud.

Azure Stream Analytics sur IoT Edge s’exécute dans le framework [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Une fois que le travail est créé dans Stream Analytics, vous pouvez le déployer et le gérer à l’aide d’IoT Hub.

## <a name="common-scenarios"></a>Scénarios courants

Cette section décrit les scénarios courants pour Stream Analytics sur IoT Edge. Le diagramme suivant illustre le déroulement des données entre les appareils IoT et le Cloud Azure.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagramme IoT Edge haut niveau":::

### <a name="low-latency-command-and-control"></a>Contrôle et commande de faible latence

Les systèmes de sécurité de fabrication doivent répondre aux données opérationnelles avec une latence très faible. Avec Stream Analytics sur IoT Edge, vous pouvez analyser les données de capteur quasiment en temps réel et émettre des commandes lorsque vous détectez des anomalies pour arrêter une machine ou déclencher des alertes.

### <a name="limited-connectivity-to-the-cloud"></a>Connectivité au cloud limitée

les systèmes stratégiques, tels que les équipements miniers à distance, les navires connectés ou les installations de forage offshore, ont besoin d’analyser les données et d’y réagir, même lorsque la connectivité au cloud est intermittente. Avec Stream Analytics, votre logique de diffusion en continu s’exécute indépendamment de la connectivité réseau et vous pouvez choisir ce que vous envoyez sur le cloud pour un traitement ultérieur ou pour y être stocké.

### <a name="limited-bandwidth"></a>Bande passante limitée

le volume de données produites par les moteurs à réaction ou par les voitures connectées peut être tellement important que les données doivent être filtrées ou traitées au préalable avant d’être envoyées vers le cloud. À l’aide de Stream Analytics, vous pouvez filtrer ou agréger les données qui doivent être envoyées vers le cloud.

### <a name="compliance"></a>Compatibilité

pour obtenir une conformité réglementaire, certaines données peuvent être rendues anonymes localement ou agrégées avant d’être envoyés vers le cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Tâches de périphérie dans Azure Stream Analytics

Les tâches Stream Analytics Edge s’exécutent dans des conteneurs déployés sur des [appareils Azure IoT Edge](../iot-edge/about-iot-edge.md). Les tâches Edge sont composées de deux parties :

* Une partie cloud qui est responsable de la définition de tâche : les utilisateurs définissent des entrées, des sorties, des requêtes et d’autres paramètres comme les événements en désordre dans le cloud.

* Un module en cours d’exécution sur vos appareils IoT. Le module contient le moteur de Stream Analytics et reçoit la définition du travail à partir du Cloud. 

Stream Analytics utilise IoT Hub pour déployer des tâches de périphérie sur les périphériques. Pour plus d’informations, consultez [Déploiement d’IoT Edge](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Travail de périphérie Azure Stream Analytics":::

## <a name="edge-job-limitations"></a>Limites des tâches Edge

L’objectif est d’obtenir la parité entre les tâches IoT Edge et les tâches cloud. La plupart des fonctionnalités du langage de requête SQL sont prises en charge pour les départements et le Cloud. Cependant, les fonctionnalités suivantes ne sont pas prises en charge pour les tâches de périphérie :
* Fonctions définies par l’utilisateur en JavaScript. Des fonctions définies par l’utilisateur sont disponibles en [C# pour les tâches IoT Edge](./stream-analytics-edge-csharp-udf.md) (préversion).
* Agrégats définis par l’utilisateur – (UDA).
* Fonctions Azure ML.
* Format AVRO pour l’entrée/sortie. À ce stade, seuls les formats CSV et JSON sont pris en charge.
* Les opérateurs SQL suivants :
    * PARTITION BY
    * GetMetadataPropertyValue
* Stratégie d’arrivée tardive

### <a name="runtime-and-hardware-requirements"></a>Runtime et conditions matérielles requises
Pour exécuter Stream Analytics sur IoT Edge, vous avez besoin d’appareils pouvant exécuter [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Stream Analytics et Azure IoT Edge utilisent des conteneurs **Docker** pour fournir une solution portable s’exécutant sur plusieurs systèmes d’exploitation hôtes (Windows, Linux).

Stream Analytics sur IoT Edge est mis à disposition sous forme d’images Windows et Linux, s’exécutant sur les architectures x86-64 ou ARM (Advanced RISC Machines). 


## <a name="input-and-output"></a>Entrée et sortie

Les tâches Stream Analytics Edge peuvent obtenir des entrées et sorties à partir d’autres modules qui s’exécutent sur des appareils IoT Edge. Pour vous connecter à partir de modules spécifiques et à ces derniers, vous pouvez définir la configuration de routage au moment du déploiement. Pour plus d’informations, consultez [la documentation de composition du module IoT Edge](../iot-edge/module-composition.md).

Les formats CSV et JSON sont pris en charge pour les entrées et sorties.

Pour chaque flux d’entrée et de sortie que vous créez dans votre tâche Stream Analytics, un point de terminaison correspondant est créé dans votre module déployé. Ces points de terminaison sont utilisables dans les itinéraires de votre déploiement.

Types d’entrée de flux pris en charge :
* Hub Edge
* Event Hub
* IoT Hub

Types de sortie de flux pris en charge :
* Hub Edge
* SQL Database
* Event Hub
* Stockage Blob/ADLS Gen2

L’entrée de référence prend en charge le type de fichier de référence. D’autres sorties peuvent être atteintes à l’aide d’un travail cloud en aval. Par exemple, un travail Stream Analytics hébergé dans Edge envoie la sortie à Edge Hub, qui peuvent ensuite envoyer la sortie à IoT Hub. Vous pouvez utiliser un deuxième travail Azure Stream Analytics Analytique hébergé sur le cloud avec une entrée d’IoT Hub et une sortie vers Power BI ou un autre type de sortie.

## <a name="license-and-third-party-notices"></a>Licence et mentions tierces
* [Licence Azure Stream Analytics sur IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Mentions tierces pour Azure Stream Analytics sur IoT Edge, version](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informations sur l’image de module Azure Stream Analytics 

La dernière mise à jour des informations de version a été effectuée le 21-09-2020 :

- Image : `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - image de base : mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - plateforme :
      - architecture : amd64
      - système d’exploitation : linux
 
- Image : `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - image de base : mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - plateforme :
      - architecture : arm
      - système d’exploitation : linux
 
- Image : `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - image de base : mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - plateforme :
      - architecture : arm64
      - système d’exploitation : linux
      
      
## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide supplémentaire, consultez notre [page de questions Microsoft Q&R sur Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Étapes suivantes

* [Plus d’informations sur Azure Iot Edge](../iot-edge/about-iot-edge.md)
* [Tutoriel Stream Analytics sur IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Développer des travaux Edge Stream Analytics avec les outils Visual Studio](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implémenter CI/CD pour Stream Analytics à l’aide d’API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
