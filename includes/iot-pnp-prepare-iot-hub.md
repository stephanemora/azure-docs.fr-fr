---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234176"
---
## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../articles/iot-hub/iot-hub-create-using-cli.md).

Si vous utilisez Azure CLI localement, commencez par vous connecter à votre abonnement Azure avec `az login`. Si vous exécutez ces commandes dans Azure Cloud Shell, vous êtes automatiquement connecté.

Si vous utilisez Azure CLI localement, la version de `az` doit être au minimum **2.0.73** ; Azure Cloud Shell utilise la version la plus récente. Utilisez la commande `az --version` pour vérifier la version installée sur votre ordinateur.

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance :

```azurecli-interactive
az extension add --name azure-iot
```

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez les espaces réservés **YourIoTHubName** et **YourDeviceID** par le _nom de hub IoT_ et l’_ID d’appareil_ de votre choix.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Exécutez la commande suivante pour obtenir la _chaîne de connexion d’appareil_  pour l’appareil que vous venez d’inscrire (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
