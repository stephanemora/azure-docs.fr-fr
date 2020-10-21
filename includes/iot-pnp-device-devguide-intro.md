---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579136"
---
IoT Plug-and-Play permet de créer des smart devices qui publient leurs fonctionnalités dans les applications Azure IoT. Les appareils IoT Plug-and-Play n’ont pas besoin de configuration manuelle quand un client les connecte à des applications compatibles avec IoT Plug-and-Play.

Un smart device peut être implémenté directement. Utilisez les [modules](../articles/iot-hub/iot-hub-devguide-module-twins.md) ou les [modules IoT Edge](../articles/iot-edge/about-iot-edge.md).

Ce guide décrit les étapes de base nécessaires pour créer un appareil, un module ou un module IoT Edge qui suit les [conventions IoT Plug-and-Play](../articles/iot-pnp/concepts-convention.md).

Pour créer un appareil, un module ou un module IoT Edge IoT Plug and Play, procédez comme suit :

1. Vérifiez que votre appareil utilise le protocole MQTT ou MQTT sur WebSockets pour se connecter à Azure IoT Hub.
1. Créez un modèle [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) pour décrire votre appareil. Pour plus d’informations, consultez [Présentation des composants dans les modèles IoT Plug-and-Play](../articles/iot-pnp/concepts-components.md).
1. Mettez à jour votre appareil ou module de façon à annoncer `model-id` dans le cadre de la connexion de l’appareil.
1. Implémentez les données de télémétrie, les propriétés et les commandes suivant les [conventions IoT Plug-and-Play](../articles/iot-pnp/concepts-convention.md).

Une fois que l’implémentation de votre appareil ou module est prête, utilisez [Azure IoT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) pour confirmer que l’appareil respecte les conventions IoT Plug-and-Play.
