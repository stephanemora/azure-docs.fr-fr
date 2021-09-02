---
title: Résolution de l'erreur Azure IoT Hub 409001 DeviceAlreadyExists
description: Correction de l'erreur 409001 DeviceAlreadyExists
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: f6ef32537da22324ac2d4991fac9df785374e16f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829198"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Cet article décrit les causes et solutions des erreurs **409001 DeviceAlreadyExists**.

## <a name="symptoms"></a>Symptômes

Lorsque vous tentez d'inscrire un appareil dans IoT Hub, la requête échoue et vous recevez l'erreur **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Cause

Un autre appareil possède déjà la même identité dans IoT Hub. 

## <a name="solution"></a>Solution

Utilisez une autre identité d'appareil et réessayez.