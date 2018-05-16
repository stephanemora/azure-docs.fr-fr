---
title: 'Azure IoT Hub : mise en route de la connexion des appareils IoT au cloud | Microsoft Docs'
description: Découvrez comment connecter vos starter kits et cartes IoT à Azure IoT Hub. Vos appareils peuvent envoyer des données de télémétrie à IoT Hub, et IoT Hub peut surveiller et gérer vos appareils.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
keywords: Tutoriel Azure IoT Hub
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dafb8aca34a5a41f45f76d526aa3b8f3b1b792c4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Bien démarrer avec les appareils réels connectés à Azure IoT Hub

Vous pouvez utiliser Azure IoT Hub et les Kits de développement logiciel (SDK) Azure IoT device pour générer des solutions Internet des objets (IoT) :

* Azure IoT Hub est un service entièrement géré dans le cloud qui connecte, surveille et gère vos appareils IoT en toute sécurité. Utilisez les Kits de développement (SDK) d’appareil Azure IoT pour implémenter vos appareils IoT.
* Utilisez une passerelle IoT dans des scénarios IoT plus complexes. Par exemple, dans les cas où vous devez tenir compte de facteurs tels que des périphériques d’ancienne génération, des coûts de bande passante, des stratégies de sécurité et de confidentialité ou le traitement de données edge. Dans ces scénarios, utilisez [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) pour implémenter une passerelle qui connecte des appareils à votre hub IoT.

## <a name="what-the-how-to-articles-cover"></a>Éléments abordés dans les articles de guide pratique

Ces articles présentent Azure IoT Hub et les kits SDK d’appareil. Les articles abordent des scénarios IoT courants pour faire la démonstration des fonctionnalités IoT Hub. Ils illustrent également la manière dont IoT Hub peut être combiné avec d’autres services et outils Azure pour créer des solutions IoT plus puissantes. Dans les articles, vous utilisez de vrais appareils IoT.

## <a name="set-up-your-device"></a>Configurer votre appareil

Connecter un appareil ou une passerelle IoT à Azure IoT Hub :

| Appareil IoT                       | Langage de programmation |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino dans VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Simulateur d’appareil en ligne         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
