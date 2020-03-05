---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 3f3e7c54ec59c42ed102da682fc9aa077709ef3f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262025"
---
## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

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