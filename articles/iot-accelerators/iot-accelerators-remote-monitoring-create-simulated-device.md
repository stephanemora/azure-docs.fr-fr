---
title: Simulation d’appareil avec surveillance à distance IoT - Azure | Microsoft Docs
description: Ce guide pratique montre comment utiliser le simulateur d’appareil avec l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 81efd9dc5d33ed23574b1cb66f26ccb444d5a3ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180532"
---
# <a name="create-and-test-a-new-simulated-device"></a>Créer et tester un appareil simulé

L’accélérateur de solution de supervision à distance vous permet de définir vos propres appareils simulés. Cet article montre comment définir un nouvel appareil Ampoule simulé, et le tester localement. L’accélérateur de solution inclut des appareils simulés tels que des réfrigérateurs et des camions. Vous pouvez cependant définir vos propres appareils simulés pour tester vos solutions IoT avant de déployer des appareils réels.

> [!NOTE]
> Cet article décrit comment utiliser des appareils simulés hébergés dans le service de simulation d’appareil. Si vous voulez créer un appareil réel, consultez [Connecter votre appareil à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).

Ce guide montre comment personnaliser le microservice de simulation d’appareil. Ce microservice fait partie de l’accélérateur de solution de supervision à distance. Pour illustrer les capacités de simulation d’appareil, ce guide pratique utilise deux scénarios dans l’application IoT Contoso :

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment créer des types d’appareils simulés personnalisés, et les tester en exécutant le microservice de simulation d’appareil localement.

L’étape suivante suggérée est d’apprendre à déployer vos types d’appareils simulés personnalisés sur l’[accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
