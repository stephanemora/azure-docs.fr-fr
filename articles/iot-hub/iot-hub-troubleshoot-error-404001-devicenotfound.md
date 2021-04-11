---
title: Résolution de l’erreur Azure IoT Hub 404001 DeviceNotFound
description: Comprendre comment corriger l’erreur 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061279"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Cet article décrit les causes et solutions des erreurs **404001 DeviceNotFound**.

## <a name="symptoms"></a>Symptômes

Au cours d’une communication cloud-à-appareil (C2D) telle qu’un message C2D, une mise à jour de jumeau ou une méthode directe, l’opération échoue en générant l’erreur **404001 DeviceNotFound**.

## <a name="cause"></a>Cause

L’opération a échoué parce que IoT Hub ne peut pas trouver l’appareil. L’appareil n’est pas inscrit ou est désactivé.

## <a name="solution"></a>Solution

Inscrivez l’ID d’appareil que vous avez utilisé, puis réessayez.