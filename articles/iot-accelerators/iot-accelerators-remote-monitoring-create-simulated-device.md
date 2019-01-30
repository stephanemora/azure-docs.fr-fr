---
title: Simulation d’appareil avec surveillance à distance IoT - Azure | Microsoft Docs
description: Ce guide pratique montre comment utiliser le simulateur d’appareil avec l’accélérateur de solution Surveillance à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/28/2018
ms.topic: conceptual
ms.openlocfilehash: 7a7a32cf1d67e9a4bbe49996b258164eb25c3763
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446763"
---
# <a name="create-and-test-a-new-simulated-device"></a>Créer et tester un appareil simulé

L’accélérateur de solution Surveillance à distance vous permet de définir vos propres appareils simulés. Cet article montre comment définir un nouvel appareil Ampoule simulé, et le tester localement. L’accélérateur de solution inclut des appareils simulés tels que des réfrigérateurs et des camions. Vous pouvez cependant définir vos propres appareils simulés pour tester vos solutions IoT avant de déployer des appareils réels.

> [!NOTE]
> Cet article décrit comment utiliser des appareils simulés hébergés dans le service de simulation d’appareil. Si vous voulez créer un appareil réel, consultez [Connecter votre appareil à l’accélérateur de solution Supervision à distance](iot-accelerators-connecting-devices.md).

Ce guide montre comment personnaliser le microservice de simulation d’appareil. Ce microservice fait partie de l’accélérateur de solution Surveillance à distance. Pour illustrer les capacités de simulation d’appareil, ce guide pratique utilise deux scénarios dans l’application IoT Contoso :

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment créer des types d’appareils simulés personnalisés, et les tester en exécutant le microservice de simulation d’appareil localement.

L’étape suivante suggérée est d’apprendre à déployer vos types d’appareils simulés personnalisés sur l’[accélérateur de solution Surveillance à distance](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
