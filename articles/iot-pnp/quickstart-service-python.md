---
title: Interagir avec un appareil IoT Plug-and-Play connecté à une solution Azure IoT (Python) | Microsoft Docs
description: Utilisez Python pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution Azure IoT et pour interagir avec lui.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574962"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play connecté à votre solution (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec le modèle d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce guide de démarrage rapide vous montre comment utiliser Python pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pour suivre ce guide de démarrage rapide, vous avez besoin de Python 3.7 sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [python.org](https://www.python.org/). Vous pouvez vérifier votre version de Python à l’aide de la commande suivante :  

```cmd/sh
python --version
```

Le package **azure-iot-device** est publié en tant que PIP.

Dans votre environnement Python local, installez le package comme suit :

```cmd/sh
pip install azure-iot-device
```

Installez le package **azure-iot-hub** en exécutant la commande suivante :

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Pour en savoir plus sur l’exemple de configuration, consultez l’[exemple de fichier Lisez-moi](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Dans ce guide de démarrage rapide, vous utilisez un exemple d’appareil à thermostat, écrit en Python, en tant qu’appareil IoT Plug-and-Play. Pour exécuter l’exemple d’appareil :

1. Ouvrez une fenêtre de terminal dans un dossier de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub du [SDK Python Azure IoT](https://github.com/Azure/azure-iot-sdk-python) à cet emplacement :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Cette fenêtre de terminal est utilisée en tant que terminal **device**. Accédez au dossier de votre dépôt cloné, puis accédez au dossier */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Pour exécutez l’exemple d’appareil à thermostat, utilisez la commande suivante :

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Des messages s’affichent pour indiquer que l’appareil a envoyé des informations et qu’il s’est signalé comme étant en ligne. Ces messages indiquent que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés. Ne fermez pas ce terminal. Vous en aurez besoin pour confirmer que l’exemple de service fonctionne également.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans ce guide de démarrage rapide, vous utilisez un exemple de solution IoT en Python pour interagir avec l’exemple d’appareil que vous venez de configurer.

1. Ouvrez une autre fenêtre de terminal à utiliser comme terminal **service**. 

1. Accédez au dossier */azure-iot-sdk-python/azure-iot-hub/samples* du référentiel du SDK Python cloné.

1. Dans le dossier Samples, il y a quatre exemples de fichiers illustrant les opérations avec la classe Digital Twin Manager : *get_digital_twin_sample.py, update_digitial_twin_sample.py, invoke_command_sample.py et invoke_component_command_sample-.py*.  Ces exemples montrent comment utiliser chaque API pour interagir avec les appareils IoT Plug-and-Play :

### <a name="get-digital-twin"></a>Obtenir le jumeau numérique

Dans [Configurer votre environnement pour les guides de démarrages rapides et tutoriels IoT Plug-and-Play](set-up-environment.md), vous avez créé deux variables d’environnement pour configurer l’exemple afin qu’il se connecte à votre IoT Hub et à votre appareil :

* **IOTHUB_CONNECTION_STRING** : la chaîne de connexion que vous avez notée précédemment.
* **IOTHUB_DEVICE_ID** : `"my-pnp-device"`.

Utilisez la commande suivante dans le terminal **service** pour exécuter cet exemple :

```cmd/sh
python get_digital_twin_sample.py
```

La sortie affiche le jumeau numérique de l’appareil et son ID de modèle :

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

L’extrait de code suivant montre l’exemple de code de *get_digital_twin_sample.py* :

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

Cet exemple montre comment utiliser un *correctif* pour mettre à jour les propriétés par le biais du jumeau numérique de votre appareil. L’extrait de code suivant de *update_digital_twin_sample.py* montre comment construire le patch :

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Utilisez la commande suivante dans le terminal **service** pour exécuter cet exemple :

```cmd/sh
python update_digital_twin_sample.py
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

Pour appeler une commande, exécutez l’exemple *invoke_command_sample.py*. Cet exemple montre comment appeler une commande dans un appareil à thermostat simple. Avant d’exécuter cet exemple, définissez les variables d’environnement `IOTHUB_COMMAND_NAME` et `IOTHUB_COMMAND_PAYLOAD` dans le terminal **service**  :

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Dans le terminal **service**, utilisez la commande suivante pour exécuter l’exemple :
  
```cmd/sh
python invoke_command_sample.py
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

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour découvrir plus d’informations sur les modèles d’appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation d’IoT Plug-and-Play](concepts-developer-guide-device-csharp.md)
