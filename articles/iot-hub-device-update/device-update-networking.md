---
title: Exigences réseau pour le service Device Update pour IoT Hub | Microsoft Docs
description: Le service Device Update pour IoT Hub utilise un grand nombre de ports réseau à des fins différentes.
author: vimeht
ms.author: vimeht
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aeeffa55a4f7f90742616e137328948f3ffa7a5c
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525417"
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
