---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152031"
---
## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell :

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez les espaces réservés **YourIoTHubName** et **YourDeviceID** par le _nom du hub IoT_ et l’_ID d’appareil_ de votre choix.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Exécutez la commande suivante pour obtenir la _chaîne de connexion d’appareil_  pour l’appareil que vous venez d’inscrire (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```