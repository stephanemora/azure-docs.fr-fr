---
title: Résolution de l’erreur Azure IoT Hub 403004 DeviceMaximumQueueDepthExceeded
description: Comprendre comment corriger l’erreur 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960326"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Cet article décrit les causes et solutions des erreurs **403004 DeviceMaximumQueueDepthExceeded**.

## <a name="symptoms"></a>Symptômes

Lorsque vous essayez d’envoyer un message cloud-à-appareil, la demande échoue avec l’erreur **403004** ou **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Cause :

La cause sous-jacente est que le nombre de messages mis en file d’attente pour l’appareil dépasse la [limite de file d’attente (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

La raison la plus probable pour laquelle vous atteignez cette limite est que vous utilisez le protocole HTTPs pour recevoir le message, ce qui entraîne une interrogation continue à l’aide de `ReceiveAsync` qui a pour effet qu’IoT Hub limite la demande.

## <a name="solution"></a>Solution

Le modèle de prise en charge des messages cloud-à-appareil avec HTTPS est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (moins de toutes les 25 minutes). Afin de réduire la probabilité d’atteindre la limite de file d’attente, basculez vers AMQP ou MQTT pour les messages cloud-à-appareil.

Vous pouvez également améliorer la logique côté appareil afin de traiter (compléter, rejeter ou abandonner) rapidement les messages en file d’attente, raccourcir leur durée de vie ou envisager d’envoyer moins de messages. Voir [Durée de vie du message C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Enfin, songez à utiliser l’[API de purge de la file d’attente](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) pour nettoyer périodiquement les messages en attente avant que la limite soit atteinte.