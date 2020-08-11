---
title: Utiliser MQTT pour créer un client d’appareil IoT Plug-and-Play en préversion | Microsoft Docs
description: Utiliser les protocole MQTT directement pour créer un client d’appareil IoT Plug-and-Play en préversion sans utiliser les kits Azure IoT Device SDK
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535180"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>Utiliser MQTT pour développer un client d’appareil IoT Plug-and-Play en préversion

Dans la mesure du possible, vous devez utiliser l’un des kits Azure IoT Device SDK pour créer des clients d’appareil IoT Plug-and-Play. Toutefois, dans certains scénarios, par exemple quand la mémoire de l’appareil utilisé est restreinte, vous devrez peut-être utiliser une bibliothèque MQTT pour communiquer avec votre hub IoT.

L’exemple de ce tutoriel utilise la bibliothèque MQTT [Eclipse Mosquitto](http://mosquitto.org/) et Visual Studio. Les étapes de ce tutoriel supposent que vous utilisez Windows sur votre ordinateur de développement.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel sur Windows, vous devez installer les logiciels suivants sur votre environnement Windows local :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : veillez à inclure la charge de travail **Développement Desktop en C++** quand vous [installez](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Explorateur Azure IoT](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour récupérer la signature d’accès partagé afin de connecter l’appareil à votre hub. Prenez note de cette chaîne, car vous l’utiliserez plus loin dans ce tutoriel :

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Utilisez la chaîne de connexion du hub IoT pour configurer l’outil **explorateur Azure IoT** :

1. Lancez l’outil **explorateur Azure IoT**.
1. Dans la page **Paramètres**, collez la chaîne de connexion du hub IoT dans les paramètres **Configurations de l’application**.
1. Sélectionnez **Enregistrer et connecter**.
1. L’appareil que vous avez ajouté précédemment figure dans la liste d’appareils de la page principale.

## <a name="clone-sample-repo"></a>Cloner l’exemple de dépôt

Utilisez la commande suivante pour cloner l’exemple de dépôt à un emplacement approprié sur votre ordinateur local :

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Installer la bibliothèque MQTT

Utilisez l’outil `vcpkg` pour télécharger et générer la bibliothèque Eclipse Mosquitto.

Utilisez la commande suivante pour cloner le dépôt **Vcpkg** à un emplacement approprié sur votre ordinateur local :

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Utilisez les commandes suivantes pour préparer l’environnement `vcpkg`. Vous avez besoin d’une invite de commandes avec élévation de privilèges quand vous exécutez `vcpkg integrate install` :

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Utilisez la commande suivante pour télécharger et générer la bibliothèque Eclipse Mosquitto :

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrer l’exemple vers IoT Plug-and-Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Examiner l’exemple de code non-IoT Plug-and-Play

Mettez à jour le code avec les détails de votre hub IoT et de votre appareil avant de le générer et de l’exécuter.

Pour voir l’exemple de code dans Visual Studio, ouvrez le fichier solution *MQTTWin32.sln* situé dans le dossier *IoTMQTTSample\src\Windows*.

Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **TelemetryMQTTWin32**, puis sélectionnez **Définir comme projet de démarrage**.

Dans le projet **TelemetryMQTTWin32**, ouvrez le fichier source **MQTT_Mosquitto.cpp**. Mettez à jour les définitions d’informations de connexion avec les détails de l’appareil que vous avez notés précédemment. Remplacez l’espace réservé de la chaîne de jeton par les éléments suivants :

* `IOTHUBNAME` : identificateur avec le nom du hub IoT que vous avez créé
* `DEVICEID` : identificateur avec le nom de l’appareil que vous avez créé
* `PWD` : identificateur avec la valeur de signature d’accès partagé que vous avez générée pour l’appareil

Vérifiez que le code fonctionne correctement en démarrant l’explorateur Azure IoT et en commençant à écouter la télémétrie.

Exécutez l’application (Ctrl+F5). Au bout de quelques secondes, vous voyez une sortie ressemblant à ce qui suit :

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Sortie de l’exemple d’application MQTT":::

Dans l’explorateur Azure IoT, vous pouvez voir que l’appareil n’est pas un appareil IoT Plug-and-Play :

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Appareil non-IoT Plug-and-Play dans l’explorateur Azure IoT":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Convertir l’appareil en appareil IoT Plug-and-Play

Un appareil IoT Plug-and-Play doit respecter un ensemble de conventions simples. Si un appareil envoie un ID de modèle quand il se connecte, il devient un appareil IoT Plug-and-Play.

Dans cet exemple, vous ajoutez un ID de modèle** au paquet de connexion MQTT. Vous transmettez l’ID de modèle comme paramètre de chaîne de requête dans `USERNAME` et remplacez l’`api-version` par `2020-05-31-preview` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Regénérez l’exemple et exécutez-le.

Le jumeau d’appareil inclut désormais l’ID de modèle :

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Voir l’ID de modèle dans l’explorateur Azure IoT":::

Vous pouvez maintenant naviguer dans le composant IoT Plug-and-Play :

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Voir les composants dans l’explorateur Azure IoT":::

Vous pouvez maintenant modifier votre code d’appareil pour implémenter la télémétrie, les propriétés et les commandes définies dans votre modèle. Pour voir un exemple d’implémentation de l’appareil à thermostat avec la bibliothèque Mosquitto, consultez [Using MQTT PnP with Azure IoTHub without the IoT SDK on Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) (Utilisation de MQTT PnP avec Azure IoT Hub sans le SDK IoT sur Windows) sur GitHub.

> [!NOTE]
> Par défaut, une signature d’accès partagé est valide pendant 60 minutes uniquement.

> [!NOTE]
>Le client utilise le fichier de certificat racine `IoTHubRootCA_Baltimore.pem` pour vérifier l’identité du hub IoT auquel il se connecte.

### <a name="mqtt-topics"></a>Rubriques MQTT

Les définitions suivantes s’appliquent aux rubriques MQTT utilisées par l’appareil pour envoyer des informations au hub IoT. Pour en savoir plus, consultez [Communication avec votre IoT Hub à l’aide du protocole MQTT](../iot-hub/iot-hub-mqtt-support.md) :

* `DEVICE_CAPABILITIES_MESSAGE` définit la rubrique utilisée par l’appareil pour signaler les interfaces qu’il implémente.
* `DEVICETWIN_PATCH_MESSAGE` définit la rubrique utilisée par l’appareil pour signaler les mises à jour de propriétés au hub IoT.
* `DEVICE_TELEMETRY_MESSAGE` définit la rubrique utilisée par l’appareil pour envoyer les données de télémétrie au hub IoT.

Pour plus d’informations sur MQTT, consultez le dépôt GitHub [MQTT Samples for Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) (Exemples MQTT pour Azure IoT).

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à modifier un client d’appareil MQTT pour qu’il respecte les conventions IoT Plug-and-Play. Pour en savoir plus sur la préversion d’IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Architecture](concepts-architecture.md)

Pour en savoir plus sur la prise en charge du protocole MQTT par IoT Hub, consultez :

> [!div class="nextstepaction"]
> [Communication avec votre IoT Hub à l’aide du protocole MQTT](../iot-hub/iot-hub-mqtt-support.md)
