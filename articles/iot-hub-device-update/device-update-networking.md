---
title: Exigences réseau pour le service Device Update pour IoT Hub | Microsoft Docs
description: Le service Device Update pour IoT Hub utilise un grand nombre de ports réseau à des fins différentes.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678375"
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

[Découvrez-en plus](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) sur la liste actuelle des protocoles pris en charge.
