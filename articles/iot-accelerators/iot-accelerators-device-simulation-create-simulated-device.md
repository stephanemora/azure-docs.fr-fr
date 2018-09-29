---
title: Simulation d’appareil avec accélérateurs de solution IoT - Azure | Microsoft Docs
description: Ce guide pratique montre comment utiliser le simulateur d’appareil avec l’accélérateur de solution Simulation d’appareil.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/28/2018
ms.topic: conceptual
ms.openlocfilehash: a2bd69a8097b05fc8a5b09c9009d0e1c1669069c
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434719"
---
# <a name="create-and-test-a-new-simulated-device"></a>Créer et tester un appareil simulé

L’accélérateur de solution Simulation d’appareil vous permet de définir vos propres appareils simulés. Cet article montre comment définir un nouvel appareil Ampoule simulé, et le tester localement. L’accélérateur de solution inclut des appareils simulés tels que des réfrigérateurs et des camions. Vous pouvez cependant définir vos propres appareils simulés pour tester vos solutions IoT avant de déployer des appareils réels.

Ce guide montre comment personnaliser le microservice de simulation d’appareil. Ce microservice fait partie de l’accélérateur de solution Simulation d’appareil. Pour illustrer les capacités de simulation d’appareil, ce guide pratique utilise deux scénarios dans l’application IoT Contoso :

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment créer des types d’appareils simulés personnalisés, et les tester en exécutant le microservice de simulation d’appareil localement.

L’étape suivante suggérée est d’apprendre à déployer vos types d’appareils simulés personnalisés sur l’[accélérateur de solution Simulation d’appareil](iot-accelerators-device-simulation-deploy-simulated-device.md).
