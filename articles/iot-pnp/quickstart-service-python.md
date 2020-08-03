---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) connecté à une solution Azure IoT (Python) | Microsoft Docs
description: Utilisez Python pour vous connecter à un appareil IoT Plug-and-Play en préversion connecté à votre solution Azure IoT et pour interagir avec lui.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352654"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

La préversion d’IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec le modèle d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce guide de démarrage rapide vous montre comment utiliser Python pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous avez besoin de Python 3.7 sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [python.org](https://www.python.org/). Vous pouvez vérifier votre version de Python à l’aide de la commande suivante :  

```cmd/sh
python --version
```

Installez le [package du SDK de service Python en préversion](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) en exécutant la commande suivante :

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce guide de démarrage rapide :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce guide de démarrage rapide :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Dans ce guide de démarrage rapide, vous utilisez un exemple d’appareil à thermostat, écrit en Python, en tant qu’appareil IoT Plug-and-Play. Pour exécuter l’exemple d’appareil :

1. Ouvrez une fenêtre de terminal dans un dossier de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub du [SDK Python Azure IoT](https://github.com/Azure/azure-iot-sdk-python) à cet emplacement :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Cette fenêtre de terminal est utilisée en tant que terminal **device**. Accédez au dossier de votre dépôt cloné, puis accédez au dossier */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Pour exécuter l’exemple d’appareil à thermostat, utilisez la commande suivante :

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Des messages s’affichent pour indiquer que l’appareil a envoyé des informations et qu’il s’est signalé comme étant en ligne. Ces messages indiquent que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés. Ne fermez pas ce terminal. Vous en aurez besoin pour confirmer que l’exemple de service fonctionne également.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans ce guide de démarrage rapide, vous utilisez un exemple de solution IoT en Python pour interagir avec l’exemple d’appareil que vous venez de configurer.

1. Ouvrez une autre fenêtre de terminal à utiliser comme terminal **service**. Le SDK du service étant en préversion, vous devez cloner les exemples à partir d’une [branche de préversion du SDK Python](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Accédez au dossier de cette branche de dépôt clonée, puis accédez au dossier */azure-iot-sdk-python/azure-iot-hub/samples*.

1. Configurez les variables d’environnement pour votre ID d’appareil et la _chaîne de connexion IoT Hub_ :

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. Le dossier samples comporte quatre exemples de fichiers dont le nom commence par `pnp`. Ces exemples montrent comment utiliser chaque API pour interagir avec les appareils IoT Plug-and-Play :

### <a name="get-digital-twin"></a>Obtenir le jumeau numérique

Utilisez la commande suivante dans le terminal **service** pour exécuter cet exemple :

```cmd/sh
python pnp_get_digital_twin_sample.py
```

La sortie affiche le jumeau numérique de l’appareil et son ID de modèle :

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

L’extrait de code suivant montre l’exemple de code de *pnp_get_digital_twin_sample.py* :

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Mettre à jour un jumeau numérique

Cet exemple montre comment utiliser un *correctif* pour mettre à jour les propriétés par le biais du jumeau numérique de votre appareil. L’extrait de code suivant de *pnp_update_digital_twin_sample.py* montre comment construire le correctif :

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Utilisez la commande suivante dans le terminal **service** pour exécuter cet exemple :

```cmd/sh
python pnp_update_digital_twin_sample.py
```

Vous pouvez vérifier que la mise à jour est appliquée dans le terminal **device** qui affiche la sortie suivante :

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

Le terminal **service** confirme que le correctif a réussi :

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Appeler une commande

Pour appeler une commande, exécutez l’exemple *pnp_invoke_command_sample.py*. Cet exemple montre comment appeler une commande dans un appareil à thermostat simple. Avant d’exécuter cet exemple, définissez les variables d’environnement `IOTHUB_COMMAND_NAME` et `IOTHUB_COMMAND_PAYLOAD` dans le terminal **service**  :

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Dans le terminal **service**, utilisez la commande suivante pour exécuter l’exemple :
  
```cmd/sh
python pnp_invoke_command_sample.py
```

Le terminal **service** affiche un message de confirmation de l’appareil :

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

Dans le terminal **device**, vous voyez que l’appareil reçoit la commande :

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour en savoir plus sur les modèles d’appareil IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation avec la préversion d’IoT Plug-and-Play](concepts-developer-guide.md)
