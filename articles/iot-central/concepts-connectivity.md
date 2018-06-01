---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à la connectivité des appareils dans Azure IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 00b621a4635ef1ceda26772ac5876fa2599b56f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202643"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connectivité des appareils dans Azure IoT Central

Cet article présente les concepts clés relatifs à la connectivité des appareils dans Microsoft Azure IoT Central.

Tous les appareils qui se connectent à votre application Azure IoT Central se connectent aux [points de terminaison](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) qui sont exposés par le hub IoT utilisé par Azure IoT Central. IoT Hub permet des connexions scalables, sécurisées et fiables depuis vos appareils connectés.

## <a name="sdk-support"></a>Prise en charge des kits SDK

Les kits SDK d’appareil Azure offrent le moyen le plus simple d’implémenter le code sur vos appareils qui se connectent à votre application Azure IoT Central. Les kits SDK d’appareils suivants sont disponibles :

- [SDK Azure IoT pour C](https://github.com/azure/azure-iot-sdk-c)
- [SDK Azure IoT pour Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK Azure IoT pour Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK Azure IoT pour Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK Azure IoT pour .NET](https://github.com/azure/azure-iot-sdk-csharp)

Chaque appareil se connecte en utilisant une chaîne de connexion unique qui l’identifie. Un appareil peut se connecter au hub IoT où il est inscrit. Quand vous créez un appareil réel dans votre application Azure IoT Central, l’application génère une chaîne de connexion que vous devez utiliser.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Fonctionnalités du SDK et connectivité d’IoT Hub

Toutes les communications des appareils avec IoT Hub utilisent les options de connectivité IoT Hub suivantes :

- [Messages d’appareil-à-cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Jumeaux d’appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

Le tableau suivant récapitule à quelles fonctionnalités des appareils Azure IoT Central correspondent les fonctionnalités IoT Hub :

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mesure : Télémétrie | Messages d’appareil-à-cloud |
| Propriétés de l’appareil | Propriétés signalées du jumeau d’appareil |
| Paramètres | Propriétés souhaitées et signalées du jumeau d’appareil |

Pour plus d’informations sur l’utilisation des kits SDK d’appareil, consultez les articles suivants pour obtenir des exemples de code :

- [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
- [Connecter un appareil Raspberry Pi à votre application Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Connecter un appareil du kit DevDiv à votre application Azure IoT Central](howto-connect-devkit.md).

La vidéo suivante donne une vue d’ensemble de la façon de connecter un appareil réel à Azure IoT Central :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protocoles

Les kits SDK d’appareil prennent en charge les protocoles réseau suivants pour la connexion à un hub IoT :

- MQTT
- AMQP
- HTTPS

Pour plus d’informations sur les différences entre ces protocoles et des conseils sur le choix de l’un d’eux, consultez [Choisir un protocole de communication](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Si votre appareil ne peut utiliser aucun des protocoles pris en charge, vous pouvez utiliser Azure IoT Edge pour faire une conversion de protocole. IoT Edge prend en charge d’autres scénarios avancés permettant de décharger l’application Azure IoT Central de certains traitements.

## <a name="security"></a>Sécurité

Toutes les données échangées entre les appareils et votre application Azure IoT Central sont chiffrées. IoT Hub authentifie chaque demande provenant d’un appareil qui se connecte aux points de terminaison IoT Hub exposés aux appareils. Pour éviter d’échanger des informations d’identification sur le réseau, un appareil utilise des jetons signés pour s’authentifier. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Actuellement, les appareils qui se connectent à Azure IoT Central doivent utiliser des jetons SAP. Les certificats X.509 ne sont pas pris en charge pour les appareils qui se connectent à Azure IoT Central.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la connectivité des appareils dans Azure IoT Central, voici les étapes suivantes suggérées :

- [Préparer et connecter un appareil DevKit](howto-connect-devkit.md)
- [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
