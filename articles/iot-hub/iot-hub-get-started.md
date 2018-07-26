---
title: 'Azure IoT Hub : mise en route de la connexion des appareils IoT au cloud | Microsoft Docs'
description: Découvrez comment connecter vos starter kits et cartes IoT à Azure IoT Hub. Vos appareils peuvent envoyer des données de télémétrie à IoT Hub, et IoT Hub peut surveiller et gérer vos appareils.
author: dominicbetts
manager: timlt
keywords: Tutoriel Azure IoT Hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125190"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Bien démarrer avec les appareils réels connectés à Azure IoT Hub

Ces articles de procédures vous présentent Azure IoT Hub et l’exécution des kits SDK d’appareil sur des appareils réels.

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
