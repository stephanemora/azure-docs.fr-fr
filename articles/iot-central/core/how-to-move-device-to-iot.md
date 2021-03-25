---
title: Déplacer un appareil vers Azure IoT Central à partir d’IoT Hub
description: Déplacer un appareil vers Azure IoT Central à partir d’IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 543c3f1c72857098540cc2a77e8a0093b907b799
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210833"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Transférer un appareil vers Azure IoT Central à partir d’IoT Hub

*Cet article s’applique aux opérateurs et aux développeurs d’appareils.*  

Cet article explique comment transférer un appareil vers une application Azure IoT Central à partir d’un IoT Hub. 

Un appareil se connecte d’abord à un point de terminaison DPS pour récupérer les informations dont il a besoin pour se connecter à votre application. En interne, votre application IoT Central utilise un hub IoT pour gérer la connectivité des appareils.  

Un appareil peut être connecté directement à un hub IoT à l’aide d’une chaîne de connexion ou d’un DPS. Le [service Azure IoT Hub Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) est l’itinéraire pour IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Pour déplacer l’appareil vers Azure IoT Central

Pour connecter un appareil à IoT Central à partir du hub IoT, un appareil doit être mis à jour avec :

* L’[ID d’étendue](../../iot-dps/concepts-service.md) de l'application IoT Central.
* Une clé dérivée de la clé [SAS du groupe](concepts-get-connected.md) ou du [certificat X. 509](../../iot-hub/iot-hub-x509ca-overview.md)

Pour interagir avec IoT Central, un modèle d’appareil doit modéliser les propriétés/télémétrie/commandes que l’appareil implémente. Pour plus d’informations, consultez [Se connecter à IoT Central](concepts-get-connected.md) et [Que sont les modèles d’appareils ?](concepts-device-templates.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes un développeur d’appareils, nous vous suggérons les étapes suivantes :

- Consultez un exemple de code montrant comment utiliser les jetons SAS dans [Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md)
- Découvrez comment [connecter des appareils avec des certificats X.509 à l’aide du kit de développement logiciel (SDK) d’appareil Node.js pour une application IoT Central](how-to-connect-devices-x509.md)
- Découvrez comment [superviser la connectivité des appareils à l’aide d’Azure CLI](./howto-monitor-devices-azure-cli.md)
- Découvrez comment [définir un nouveau type d’appareil IoT dans votre application Azure IoT Central](./howto-set-up-template.md)
- En savoir plus sur [les appareils Azure IoT Edge et Azure IoT Central](./concepts-iot-edge.md)