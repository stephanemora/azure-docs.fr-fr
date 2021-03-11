---
title: Résolution de l’erreur Azure IoT Hub 412002 DeviceMessageLockLost
description: Comprendre comment corriger l’erreur 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435475"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Cet article décrit les causes et solutions des erreurs **412002 DeviceMessageLockLost**.

## <a name="symptoms"></a>Symptômes

Lorsque vous essayez d’envoyer un message cloud-à-appareil, la demande échoue avec l’erreur **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Cause

Lorsqu’un appareil reçoit un message cloud vers appareil à partir de la file d’attente (par exemple, à l’aide de [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync)), le message est verrouillé par IoT Hub pendant une minute. Si l’appareil tente de compléter le message au terme de ce délai de verrouillage, IoT Hub lève l'exception.

## <a name="solution"></a>Solution

Si IoT Hub n’obtient pas la notification durant le délai de verrouillage d'une minute, il rétablit l’état du message sur *En file d'attente*. L’appareil peut tenter de recevoir à nouveau le message. Pour éviter que l’erreur ne se reproduise, implémentez la logique côté appareil afin de compléter le message dans un délai d’une minute après réception. Ce délai d’attente d’une minute ne peut pas être modifié.