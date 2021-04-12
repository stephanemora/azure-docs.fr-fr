---
title: Exigences réseau pour le service Device Update pour IoT Hub | Microsoft Docs
description: Le service Device Update pour IoT Hub utilise un grand nombre de ports réseau à des fins différentes.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558378"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Ports utilisés avec le service Device Update pour IoT Hub
ADU utilise un grand nombre de ports réseau à des fins différentes.

## <a name="default-ports"></a>Ports par défaut

Objectif|Numéro de port |
---|---
Télécharger à partir de réseaux/CDN  | 80 (protocole HTTP)
Télécharger à partir de MCC/CDN | 80 (protocole HTTP)
Connexion de l’agent ADU à Azure IoT Hub  | 8883 (protocole MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Utiliser des protocoles pris en charge par Azure IoT Hub
L’agent ADU peut être modifié pour utiliser l’un des protocoles Azure IoT Hub pris en charge.

[Découvrez-en plus](../iot-hub/iot-hub-devguide-protocols.md) sur la liste actuelle des protocoles pris en charge.
