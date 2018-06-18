---
title: Comparaison entre Azure IoT Hub et Azure Event Hub | Microsoft Docs
description: Comparaison des services Azure IoT Hub et Azure Event Hubs mettant en avant les différences de fonction et des exemples d’utilisation. La comparaison inclut les protocoles pris en charge, la surveillance, la gestion des appareils et les téléchargements de fichiers.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 9ad95071de07777e38533ecec9e8558841d8b1ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633959"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Connexion des appareils IoT à Azure : IoT Hub et Event Hubs

Azure propose des services spécialement développés pour divers types de connectivité et de communication pour vous aider à connecter vos données à la puissance du cloud. Azure IoT Hub et Azure Event Hubs sont des services cloud qui peuvent ingérer de grandes quantités de données et des processus ou stocker ces données pour des perspectives. Les deux services sont similaires, car ils prennent tous deux en charge l’ingestion des données avec une latence faible et une haute fiabilité, mais ils sont conçus à des fins différentes. IoT Hub a été développé spécifiquement pour répondre aux besoins uniques de la connexion à grande échelle des appareils IoT à Azure Cloud, alors que le service Event Hubs a été conçu pour la diffusion en continu du Big Data. C’est pourquoi Microsoft recommande d’utiliser Azure IoT Hub pour connecter des appareils IoT à Azure.

Azure IoT Hub est la passerelle cloud qui connecte des appareils IoT pour collecter les données afin de stimuler perspectives et automatisation. En outre, IoT Hub inclut des fonctionnalités qui enrichissent la relation entre vos appareils et vos systèmes principaux. Les fonctionnalités de communication bidirectionnelle impliquent que si vous recevez des données provenant d’appareils, vous pouvez également renvoyer des commandes et stratégies vers les appareils pour mettre à jour des propriétés ou appeler des actions de gestion des appareils.  Cette connectivité cloud-à-appareil optimise également la fonctionnalité importante de fourniture d’intelligence du cloud à vos périphériques de périmètre à l’aide d’Azure IoT Edge. L’identité unique au niveau des appareils du service IoT Hub permet de mieux sécuriser votre solution IoT contre des attaques potentielles. 

[Azure Event Hubs][Azure Event Hubs] est le service de diffusion en continu du Big Data d’Azure. Il est conçu pour les scénarios de diffusion en continu de données haut débit dans lesquels les clients peuvent envoyer des milliards de requêtes quotidiennes. Le service Event Hubs utilise un modèle de consommateur partitionné pour augmenter la taille des instances de votre flux de données. Il est intégré au service Big Data et analytique d’Azure comprenant Databricks, Stream Analytics, ADLS et HDInsight. Avec des fonctionnalités comme Event Hubs Capture et Majoration automatique, ce service est conçu pour prendre en charge vos solutions et applications du Big Data. En outre, IoT Hub s’appuie sur le chemin d’accès au flux de données de télémétrie d’Event Hubs. Votre solution IoT bénéficie également de la puissance considérable d’Event Hubs.

En résumé, si les deux solutions sont conçues pour l’ingestion des données à une très grande échelle, seul le service IoT Hub fournit des fonctionnalités enrichies propres à IoT conçues pour vous permettre d’optimiser la valeur commerciale de la connexion de vos appareils IoT au cloud Azure.  Si vous en êtes au tout début de votre migration vers l’IoT, commencer par IoT Hub pour prendre en charge vos scénarios d’ingestion des données vous permettra de disposer d’un accès instantané aux fonctionnalités IoT complètes pour satisfaire à vos besoins commerciaux et techniques.

La table suivante fournit des détails sur le mode de comparaison entre les deux niveaux de IoT Hub et Event Hubs lorsque vous procédez à une évaluation par rapport aux capacités IoT. Pour plus d’informations sur les niveaux de base et Standard d’IoT Hub, consultez [Comment choisir le bon niveau IoT Hub][lnk-scaling].

| Fonctionnalité IoT | Niveau standard IoT Hub | Niveau de base IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Messages d’appareil-à-cloud | ![Vérification][1] | ![Vérification][1] | ![Vérification][1] |
| Protocoles : HTTPS, AMQP, AMQP sur WebSockets | ![Vérification][1] | ![Vérification][1] | ![Vérification][1] |
| Protocoles : MQTT, MQTT sur WebSockets | ![Vérification][1] | ![Vérification][1] |  |
| Identité par appareil | ![Vérification][1] | ![Vérification][1] |  |
| Fichier téléchargé à partir d’appareils | ![Vérification][1] | ![Vérification][1] |  |
| Service Device Provisioning | ![Vérification][1] | ![Vérification][1] |  |
| Messages de cloud-à-appareil | ![Vérification][1] |  |  |
| Gestion des jumeaux d'appareils et des appareils | ![Vérification][1] |  |  |
| IoT Edge | ![Vérification][1] |  |  |

Même si le seul cas d’usage est l’ingestion de données appareil-à-cloud, nous recommandons fortement l’utilisation d’IoT Hub car il fournit un service conçu pour la connectivité des appareils IoT. 

### <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez le [guide du développeur IoT Hub][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png
