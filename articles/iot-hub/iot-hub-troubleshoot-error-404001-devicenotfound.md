---
title: Résolution de l’erreur Azure IoT Hub 404001 DeviceNotFound
description: Comprendre comment corriger l’erreur 404001 DeviceNotFound
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: 127f4546d674eb425440a8e0f395ca8ff0a6b6f4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515529"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Cet article décrit les causes et solutions des erreurs **404001 DeviceNotFound**.

## <a name="symptoms"></a>Symptômes

Au cours d’une communication cloud-à-appareil (C2D) telle qu’un message C2D, une mise à jour de jumeau ou une méthode directe, l’opération échoue en générant l’erreur **404001 DeviceNotFound**.

## <a name="cause"></a>Cause

L’opération a échoué parce que IoT Hub ne peut pas trouver l’appareil. L’appareil n’est pas inscrit ou est désactivé.

## <a name="solution"></a>Solution

Inscrivez l’ID d’appareil que vous avez utilisé, puis réessayez.