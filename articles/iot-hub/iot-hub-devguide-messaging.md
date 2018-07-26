---
title: Présentation des messages Azure IoT Hub | Microsoft Docs
description: Guide du développeur - Messagerie d’appareil-à-cloud et de cloud-à-appareil avec IoT Hub. Comprend des informations sur les formats de message et les protocoles de communication pris en charge.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: b0667f820145f16c75a07ebe1849e20d2de36cc7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185507"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Messagerie d’appareil-à-cloud et de cloud-à-appareil avec IoT Hub

Utilisez la messagerie IoT Hub pour communiquer avec vos appareils en :

* envoyant des messages [appareil-à-cloud][lnk-d2c] depuis vos appareils vers le back-end de votre solution ;
* envoyant des messages [cloud-à-appareil][lnk-c2d] depuis le back-end de la solution vers vos appareils.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Les principales propriétés de la fonctionnalité de messagerie IoT Hub sont la fiabilité et la durabilité des messages. Ces propriétés activent la résilience de la connectivité intermittente côté appareils et des pics de chargement dans le traitement d’événements côté cloud. IoT Hub implémente *au moins une fois* des garanties de remise pour l’envoi de messages appareil-à-cloud et cloud-à-appareil.

Pour obtenir une présentation des fonctionnalités IoT Hub, consultez la [Présentation du service Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Quand utiliser la messagerie IoT Hub

Utilisez les messages appareil-à-cloud pour envoyer des alertes et des données de télémétrie de série chronologique à partir de votre application pour appareil, et des messages cloud-à-appareil pour envoyer des notifications unidirectionnelles à votre application pour appareil.

* Reportez-vous à [l’aide sur la communication appareil-à-cloud][lnk-d2c-guidance] en cas de doute entre l’utilisation des messages appareil-à-cloud, des propriétés signalées ou du chargement de fichiers.
* Reportez-vous à [l’aide sur la communication cloud-à-appareil][lnk-c2d-guidance] en cas de doute entre l’utilisation des messages cloud-à-appareil, des propriétés de votre choix ou des méthodes directes.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez la [messagerie appareil-à-cloud][lnk-d2c] IoT Hub.
* Découvrez la [messagerie cloud-à-appareil][lnk-c2d] IoT Hub.

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: about-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md