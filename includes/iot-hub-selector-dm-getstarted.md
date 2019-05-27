---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156356"
---
> [!div class="op_single_selector"]
> * [APPAREIL : Service Node.js : Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [APPAREIL : C#Service : C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [APPAREIL : Service Java : Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [APPAREIL : Service Python : Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Les applications principales peuvent utiliser des primitives Azure IoT Hub, comme [jumeau d’appareil][lnk-devtwin] et [méthodes directes][lnk-c2dmethod], afin de démarrer et surveiller à distance les actions de gestion d’appareil sur les appareils. Ce didacticiel illustre la manière dont une application principale et une application pour périphérique fonctionnent conjointement afin de lancer et de surveiller le redémarrage à distance d’un appareil à l’aide de IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Utilisez une méthode directe pour lancer des actions de gestion d’appareils (redémarrage, réinitialisation des paramètres d’usine et mise à jour du microprogramme) à partir d’une application principale dans le cloud. L’appareil est chargé de :

* Gérer la requête de méthode envoyée à partir d’IoT Hub.
* Démarrer l’action correspondante spécifique à l’appareil sur l’appareil.
* Fournir à IoT Hub des mises à jour de l’état via des *propriétés signalées*.

Vous pouvez utiliser une application principale dans le cloud pour exécuter des requêtes sur la représentation d’appareil afin d’indiquer la progression des actions de gestion de votre appareil.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
