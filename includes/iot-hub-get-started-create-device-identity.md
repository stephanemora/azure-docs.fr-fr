---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346203"
---
## <a name="create-a-device-identity"></a>Création d’une identité d’appareil

Dans cette section, vous utilisez Azure CLI pour créer une identité d’appareil pour ce didacticiel. Azure CLI est préinstallé dans [Azure Cloud Shell](~/articles/cloud-shell/overview.md). Vous pouvez également l’[installer localement](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Les ID d’appareil respectent la casse.

1. Exécutez la commande suivante dans l’environnement de ligne de commande au sein duquel vous utilisez Azure CLI pour installer l’extension IoT :

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Si vous exécutez Azure CLI localement, utilisez la commande suivante pour vous connecter à votre compte Azure (si vous utilisez le Cloud Shell, vous êtes automatiquement connecté et n’avez pas besoin d’exécuter cette commande) :

    ```cmd/sh
    az login
    ```

1. Enfin, créez une nouvelle identité d’appareil nommée `myDeviceId` et récupérez la chaîne de connexion d’appareil à l’aide des commandes suivantes :

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Prenez note de la chaîne de connexion de l’appareil à partir du résultat. Cette chaîne de connexion est utilisée par l’application d’appareil pour se connecter à votre IoT Hub en tant qu’appareil.

<!-- images and links -->
