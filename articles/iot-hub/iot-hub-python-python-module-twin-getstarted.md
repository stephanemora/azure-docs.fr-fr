---
title: Identité de module et jumeau de module Azure IoT Hub (Python)
description: Découvrez comment créer une identité de module et mettre à jour un jumeau de module à l’aide des SDK IoT pour Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: e18d448d9aee0137f1167d23a2bbf53486d0c764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953843"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Bien démarrer avec l’identité de module et le jumeau de module IoT Hub (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Les identités de module et les jumeaux de module](iot-hub-devguide-module-twins.md) sont similaires aux identités d’appareil et aux jumeaux d’appareil Azure IoT Hub, mais offrent un plus grand niveau de détail. Contrairement à l’identité d’appareil et au jumeau d’appareil Azure IoT Hub qui permettent à l’application principale de configurer un appareil et d’obtenir une visibilité sur l’état de l’appareil, une identité de module et un jumeau de module fournissent ces fonctionnalités pour les composants individuels d’un appareil. Sur les appareils compatibles qui intègrent plusieurs composants, par exemple des appareils basés sur un système d’exploitation ou des appareils avec un microprogramme, ils permettent d’isoler la configuration et les conditions de chacun de ces composants.
>

À la fin de ce didacticiel, vous disposerez de deux applications Python :

* **CreateIdentities**, qui crée une identité d’appareil, une identité de module et une clé de sécurité associée pour connecter votre appareil et vos clients de module.

* **UpdateModuleTwinReportedProperties**, qui envoie à votre IoT Hub les propriétés à jour de votre jumeau de module.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Créer une identité d’appareil et une identité de module dans IoT Hub

Dans cette section, vous allez créer une application Python qui crée une identité d’appareil et une identité de module dans le registre des identités de votre hub IoT. Un appareil ou un module ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Pour plus d’informations, consultez la section « Registre des identités » du [Guide du développeur IoT Hub](iot-hub-devguide-identity-registry.md).0 En exécutant cette application console, une clé et un ID uniques sont générés pour chaque appareil et module. Votre appareil et le module utilisent ces valeurs pour s’identifier lorsqu’ils envoient des messages d’appareil-à-cloud à IoT Hub. Les ID sont sensibles à la casse.

Ajoutez le code suivant à votre fichier Python :

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

Cette application crée une identité d’appareil avec l’ID **myFirstDevice** et une identité de module avec l’ID **myFirstModule** sous l’appareil **myFirstDevice**. (si cet ID de module existe déjà dans le registre d’identité, le code récupère simplement les informations existantes du module.) L’application affiche ensuite la clé primaire pour cette identité. Vous utilisez cette clé dans l’application de module simulé pour vous connecter à votre IoT Hub.

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils et des modules pour permettre un accès sécurisé à IoT Hub. Le registre des identités stocke les ID et les clés d’appareil à utiliser en tant qu’informations d’identification de sécurité. Il stocke également un indicateur activé/désactivé pour chaque appareil pouvant être utilisé pour désactiver l’accès de cet appareil. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Il n’y a aucun indicateur d’activation/désactivation pour les identités de module. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Mettre à jour le jumeau de module à l’aide du SDK d’appareil Python

Dans cette section, vous allez créer sur votre appareil simulé une application Python qui met à jour les propriétés signalées du jumeau de module.

1. **Obtenir la chaîne de connexion de votre module** : vous pouvez le faire dès maintenant si vous vous connectez au [portail Azure](https://portal.azure.com/). Accédez à votre IoT Hub, puis cliquez sur Appareils IoT. Recherchez et ouvrez myFirstDevice pour vérifier que myFirstModule a bien été créé. Copiez la chaîne de connexion du module. Vous en aurez besoin à l’étape suivante.

   ![Détails du module du Portail Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Créer l’application UpdateModuleTwinReportedProperties**

   Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Cet exemple de code montre comment récupérer le jumeau de module et mettre à jour les propriétés déclarées avec le protocole AMQP.

## <a name="get-updates-on-the-device-side"></a>Recevoir les mises à jour du côté appareil

En plus du code ci-dessus, vous pouvez ajouter le bloc de code suivant pour recevoir le message de mise à jour du jumeau sur votre appareil.

```python
import time
import threading
from azure.iot.device import IoTHubModuleClient

CONNECTION_STRING = "{deviceConnectionString}"


def twin_update_listener(client):
    while True:
        patch = client.receive_twin_desired_properties_patch()  # blocking call
        print("")
        print("Twin desired properties patch received:")
        print(patch)

def iothub_client_sample_run():
    try:
        module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

        twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
        twin_update_listener_thread.daemon = True
        twin_update_listener_thread.start()

        while True:
            time.sleep(1000000)

    except KeyboardInterrupt:
        print("IoTHubModuleClient sample stopped")


if __name__ == '__main__':
    print ( "Starting the IoT Hub Python sample..." )
    print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

    iothub_client_sample_run()
```

## <a name="next-steps"></a>Étapes suivantes

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Prise en main de la gestion d’appareils](iot-hub-node-node-device-management-get-started.md)

* [Bien démarrer avec IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)