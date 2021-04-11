---
title: Résolution de l’erreur Azure IoT Hub 404103 DeviceNotOnline
description: Comprendre comment corriger l’erreur 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061262"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Cet article décrit les causes et solutions des erreurs **404103 DeviceNotFound**.

## <a name="symptoms"></a>Symptômes

Une méthode directe d’accès à un appareil échoue en générant l’erreur **404103 DeviceNotOnline**, même si l’appareil est en ligne. 

## <a name="cause"></a>Cause

Si vous savez que l’appareil est en ligne et continuez à recevoir l’erreur, cela est probablement dû au fait que le rappel de méthode directe n’est pas inscrit sur l’appareil.

## <a name="solution"></a>Solution

Pour configurer correctement votre appareil pour les rappels de méthode directe, voir [Gérer une méthode directe sur un appareil](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).