---
title: Utiliser un IoT Hub existant avec la solution de simulation d’appareil | Microsoft Docs
description: Cet article décrit la configuration de l’accélérateur de solution de simulation d’appareil pour utiliser un IoT Hub existant.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967486"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Utiliser un IoT Hub existant avec l’accélérateur de solution de simulation d’appareil

Lorsque vous approvisionnez l’accélérateur de solution de simulation d’appareil, vous pouvez choisir de déployer un IoT Hub dans le groupe de ressources de l’accélérateur de solution à utiliser dans votre simulation.

Si vous ne choisissez pas de déployer le IoT Hub facultatif, vous devez utiliser votre propre hub pour toutes les simulations exécutées. Si vous choisissez de déployer le IoT Hub facultatif, vous pouvez choisir d’utiliser ce hub facultatif ou votre propre hub.

Si vous n’avez pas d’IoT Hub, vous pouvez toujours en créer un à partir du [portail Azure](https://portal.azure.com).

Pour utiliser un IoT Hub déjà existant, vous avez besoin d’une chaîne de connexion pour la stratégie d'accès partagé **iothubowner**. Vous pouvez obtenir cette chaîne de connexion auprès du [portail Azure](https://portal.azure.com) :

1. Dans la page de configuration du hub du portail, cliquez sur **Stratégies d’accès partagé**.
1. Cliquez sur **iothubowner**.
1. Copiez la chaîne de connexion primaire ou secondaire.

[![Obtenir la chaîne de connexion](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Utilisez la chaîne de connexion que vous avez copiée lors de la configuration de la simulation :

[![Configurer une simulation](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Grâce à ce guide de procédure, vous avez appris à utiliser un IoT Hub existant dans une simulation. Vous souhaiterez ensuite peut-être en savoir comment [configurer un modèle d’appareil personnalisé](iot-accelerators-device-simulation-custom-model.md) pour une simulation.
