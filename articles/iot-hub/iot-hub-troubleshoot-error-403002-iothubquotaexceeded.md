---
title: Résolution de l’erreur Azure IoT Hub 403002 IoTHubQuotaExceeded
description: Comprendre comment corriger l’erreur 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061313"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Cet article décrit les causes et solutions des erreurs **403002 IoTHubQuotaExceeded**.

## <a name="symptoms"></a>Symptômes

Toutes les demandes que vous adressez à IoT Hub échouent avec l’erreur **403002 IoTHubQuotaExceeded**. La liste d’appareils du hub IoT ne se charge pas dans le Portail Azure.

## <a name="cause"></a>Cause

Le quota quotidien des messages pour le hub IoT est dépassé. 

## <a name="solution"></a>Solution

[Mettez à niveau ou augmentez le nombre d’unités sur le hub IoT](iot-hub-upgrade.md) ou attendez le jour UTC suivant que le quota quotidien soit actualisé.

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre la façon dont les opérations sont comptabilisées dans le quota, telles que les requêtes de jumeaux et les méthodes directes, consultez [Comprendre la tarification d’IoT Hub](iot-hub-devguide-pricing.md#charges-per-operation)
* Pour configurer la surveillance de l’utilisation quotidienne du quota, configurez une alerte avec la métrique *Nombre total de messages utilisés*. Pour obtenir des instructions pas à pas, consultez [Configurer des métriques et des alertes avec IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)