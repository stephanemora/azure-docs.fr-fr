---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ac72360db5fb3d075b4caa3208f372f22441c7b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755578"
---
## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Pour effectuer les étapes de ce tutoriel, vous devez disposer d’un hub Azure IoT dans votre abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous utilisez Azure CLI localement, commencez par vous connecter à votre abonnement Azure avec `az login`. Si vous exécutez ces commandes dans Azure Cloud Shell, vous êtes automatiquement connecté.

Si vous utilisez Azure CLI localement, la version de `az` doit être au minimum  **2.8.0**  ; Azure Cloud Shell utilise la version la plus récente. Utilisez la commande `az --version` pour vérifier la version installée sur votre ordinateur.

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance :

```azurecli-interactive
az extension add --name azure-iot
```

Si vous n’avez pas encore de hub IoT à utiliser, exécutez les commandes suivantes pour créer un groupe de ressources et un hub IoT gratuit dans votre abonnement. Remplacez `<YourIoTHubName>` par le nom de hub souhaité :

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> IoT Plug-and-Play est actuellement disponible sur les hubs IoT créés dans les régions USA Centre, Europe Nord et Japon Est. La prise en charge d’IoT Plug-and-Play n’est pas incluse dans les hubs IoT de niveau De base.

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez les espaces réservés `<YourIoTHubName>` et `<YourDeviceID>` par le _nom de hub IoT_ et l’ _ID d’appareil_ de votre choix.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
