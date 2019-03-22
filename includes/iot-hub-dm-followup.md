---
title: Fichier Include
description: Fichier Include
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 69fdc6cf678107ef64ea1fe7b819738fd4a4ff4f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56740868"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personnaliser et étendre les actions de gestion d’appareils

Vos solutions IoT peuvent étendre l’ensemble défini de modèles de gestion d’appareils ou activer des modèles personnalisés en utilisant les primitives de la méthode cloud-à-appareil et du jumeau d’appareil. La réinitialisation des paramètres d’usine, la mise à jour du microprogramme, la mise à jour logicielle, la gestion de l’alimentation, la gestion du réseau et de la connectivité, et le chiffrement des données sont d’autres exemples d’actions de gestion des appareils.

## <a name="device-maintenance-windows"></a>Fenêtres de maintenance d’appareil

En règle générale, vous configurez des appareils pour effectuer des actions à un moment qui minimise les interruptions et les temps d’arrêt. Les fenêtres de maintenance d’appareil constituent un modèle couramment utilisé pour définir l’heure à laquelle un appareil doit mettre à jour sa configuration. Vos solutions principales peuvent utiliser les propriétés souhaitées de la représentation d’appareil pour définir et activer une stratégie sur votre appareil qui permet d’obtenir une fenêtre de maintenance. Lorsqu’un appareil reçoit la stratégie de fenêtre de maintenance, il peut utiliser la propriété signalée de la représentation d’appareil pour indiquer l’état de la stratégie. L’application principale peut ensuite utiliser des requêtes de représentation d’appareil pour certifier la conformité des appareils et de chaque stratégie.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher un redémarrage à distance sur un appareil. Vous avez utilisé les propriétés signalées pour indiquer le moment du dernier redémarrage de l’appareil et vous avez interrogé le jumeau d’appareil pour découvrir l’heure du dernier redémarrage de l’appareil à partir du cloud.

Pour continuer la mise en route avec IoT Hub et les modèles de gestion de périphérique, comme la mise à jour du microprogramme de distance, consultez [comment effectuer une mise à jour du microprogramme](../articles/iot-hub/tutorial-firmware-update.md)

Pour savoir comment étendre votre solution et planification appels de méthode sur plusieurs appareils de IoT, consultez [planifier et diffuser des travaux](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).