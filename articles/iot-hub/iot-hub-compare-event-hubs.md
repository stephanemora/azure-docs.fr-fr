---
title: Comparaison entre Azure IoT Hub et Azure Event Hub | Microsoft Docs
description: Comparaison des services Azure IoT Hub et Azure Event Hubs mettant en avant les différences de fonction et des exemples d’utilisation. La comparaison inclut les protocoles pris en charge, la surveillance, la gestion des appareils et les téléchargements de fichiers.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparaison entre Azure IoT Hub et Azure Event Hub

Azure IoT Hub et Azure Event Hubs sont des services cloud qui peuvent ingérer de grandes quantités de données et des processus ou stocker ces données pour des perspectives. Les deux services sont similaires en ce qu’ils prennent en charge le traitement d’événements et de données de télémétrie avec une latence faible et une haute fiabilité. Toutefois, seul IoT Hub a été développé avec les fonctionnalités spécifiques nécessaires pour prendre en charge des scénarios d’internet des objets à l’échelle. 

IoT Hub Azure est la passerelle cloud qui connecte des appareils et collecte les données pour des perspectives et l’automatisation. Ceci facilite la diffusion des données dans le cloud et la gestion de vos appareils à l’échelle. Un différenciateur important entre IoT Hub et d’autres services d’ingestion des données est que IoT Hub inclut des fonctionnalités qui enrichissement la relation entre vos appareils et vos systèmes principaux. Les fonctionnalités de communication bidirectionnelle signifient que pendant que vous recevez des données à partir d’appareils, vous pouvez également renvoyer des messages vers les appareils pour mettre à jour des propriétés ou appeler une action. L’identité au niveau de l’appareil permet de sécuriser votre système. Le calcul distribué déplace le service cloud logique vers des appareils Edge.

[Azure Event Hubs][Azure Event Hubs] est un service d’ingestion d’événements qui peut traiter et stocker de grandes quantités de données et la télémétrie. Event Hubs est conçu pour l’ingestion d’événements à une très grande échelle, à la fois dans le contexte de scénarios entre des centres de données et réseaux virtuels et à l’intérieur de centres de données mais ne fournit pas de fonctionnalités riches spécifiques à IoT disponibles avec IoT Hub. Pour cette raison, nous déconseillons Event Hubs pour vos solutions IoT. 

La table suivante fournit des détails sur le mode de comparaison entre les deux niveaux de IoT Hub et Event Hubs lorsque vous procédez à une évaluation par rapport aux capacités IoT. Pour plus d’informations sur les niveaux de base et Standard d’IoT Hub, consultez [Comment choisir le bon niveau IoT Hub][lnk-scaling].

| Fonctionnalité IoT | Niveau standard IoT Hub | Niveau de base IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Messages d’appareil-à-cloud | ![Vérification][1] | ![Vérification][1] | ![Vérification][1] |
| Protocoles : HTTPS, AMQP, AMQP sur WebSockets | ![Vérification][1] | ![Vérification][1] | ![Vérification][1] |
| Protocoles : MQTT, MQTT sur websockets | ![Vérification][1] | ![Vérification][1] |  |
| Identité par appareil | ![Vérification][1] | ![Vérification][1] |  |
| Fichier téléchargé à partir d’appareils | ![Vérification][1] | ![Vérification][1] |  |
| Service Device Provisioning | ![Vérification][1] | ![Vérification][1] |  |
| Messages de cloud-à-appareil | ![Vérification][1] |  |  |
| Gestion des jumeaux d'appareils et des appareils | ![Vérification][1] |  |  |
| IoT Edge | ![Vérification][1] |  |  |

Même si le seul cas d’usage est l’ingestion de données appareil-à-cloud, nous recommandons fortement l’utilisation d’IoT Hub car il fournit un service conçu pour la connectivité des appareils IoT. 

### <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez le [guide du développeur IoT Hub][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png