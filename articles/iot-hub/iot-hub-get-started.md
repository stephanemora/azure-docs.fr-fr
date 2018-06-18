---
title: 'Azure IoT Hub : mise en route de la connexion des appareils IoT au cloud | Microsoft Docs'
description: Découvrez comment connecter vos starter kits et cartes IoT à Azure IoT Hub. Vos appareils peuvent envoyer des données de télémétrie à IoT Hub, et IoT Hub peut surveiller et gérer vos appareils.
author: dominicbetts
manager: timlt
keywords: Tutoriel Azure IoT Hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634700"
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
