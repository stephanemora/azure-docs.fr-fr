---
title: Exemple de connexion d’un pont IoT Plug-and-Play s’exécutant sur Linux ou Windows à un hub IoT | Microsoft Docs
description: Créez et exécutez un exemple de code pour pont IoT Plug-and-Play sur Linux ou Windows qui se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28feaf266e2d5bed14077a686e6eb936e430a695
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066753"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Exemple de connexion d’un pont IoT Plug-and-Play s’exécutant sur Linux ou Windows à IoT Hub

Cet article montre comment créer un exemple d’adaptateur environnemental de pont IoT Plug-and-Play, comment le connecter à votre hub IoT, et comment utiliser l’outil Explorateur Azure IoT pour afficher la télémétrie qu’il envoie. Le pont IoT Plug-and-Play est écrit en C et comprend l’Azure IoT device SDK pour C. À la fin de ce didacticiel, vous devriez pouvoir exécuter le pont IoT Plug-and-Play et le voir rapporter la télémétrie dans l’Explorateur Azure IoT :

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Capture d’écran montrant l’Explorateur Azure IoT avec un tableau de télémétrie (humidité, température) rapportée du pont IoT Plug-and-Play.":::

## <a name="prerequisites"></a>Prérequis

Vous pouvez exécuter l’exemple dans l’article sur Windows ou Linux. Les commandes de l’interpréteur de commandes de ce guide pratique suivent la convention Windows des séparateurs de chemin « `\` ». Si vous suivez ce guide sur Linux, veillez à remplacer les séparateurs par « `/` ».

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

Pour interagir avec l’exemple d’appareil dans la deuxième partie de cet article, vous utilisez l’outil **Explorateur Azure IoT**. [Téléchargez et installez la dernière version de l’Explorateur Azure IoT](./howto-use-iot-explorer.md) pour votre système d’exploitation.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans cet article :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans cet article :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Télécharger et exécuter le pont

Dans cet article, vous disposez de deux options pour exécuter le pont. Vous pouvez :

- Télécharger un exécutable prédéfini et l’exécutez comme décrit dans cette section.
- Télécharger le code source, puis [générer et exécuter le pont](#build-and-run-the-bridge) comme décrit dans la section suivante.

Pour télécharger et exécuter le pont :

1. Accédez à la [page des versions](https://github.com/Azure/iot-plug-and-play-bridge/releases) d’IoT Plug-and-Play.
1. Téléchargez le fichier exécutable prégénéré pour votre système d’exploitation : **pnpbridge_bin.exe** pour Windows ou **pnpbridge_bin** pour Linux.
1. Téléchargez l’exemple de fichier de configuration [config.JSON](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) pour l’exemple de capteur environnemental. Assurez-vous que le fichier de configuration se trouve dans le même dossier que l’exécutable.
1. Modifiez le fichier *config.JSON* :

    - Ajoutez la valeur `connection-string` qui est la _chaîne de connexion de l’appareil_ que vous avez noté précédemment.
    - Ajoutez la valeur de `symmetric_key` qui est la valeur de clé d’accès partagé de la _chaîne de connexion de l’appareil_.
    - Remplacez la valeur `root_interface_model_id` par `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`.

    La première section du fichier de *config.JSON* ressemble maintenant à l’extrait de code suivant :

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Exécutez le fichier exécutable dans votre environnement de ligne de commande. Le pont génère une sortie qui ressemble à ceci :

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Générer et exécuter le pont

Si vous préférez générer le fichier exécutable vous-même, vous pouvez télécharger le code source et générer des scripts.

Ouvrez une invite de commandes dans un dossier de votre choix. Exécutez la commande suivante pour cloner le pont [IoT Plug-and-Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) et le référentiel GitHub à cet emplacement :

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Après avoir cloné le référentiel, mettez à jour les sous-modules. Les sous-modules incluent le kit de développement logiciel (SDK) Azure IoT pour C :

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Attendez-vous à ce que cette opération prenne plusieurs minutes.

> [!TIP]
> Si vous rencontrez des problèmes d’échec de la mise à jour du sous-module de clone Git, il s’agit d’un problème connu avec les chemins d’accès aux fichiers Windows. Vous pouvez essayer la commande suivante pour résoudre le problème : `git config --system core.longpaths true`

La configuration requise pour la création du pont diffère selon le système d’exploitation :

### <a name="windows"></a>Windows

Pour créer le pont d’IoT Plug-and-Play sur Windows, installez les logiciels suivants :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : veillez à inclure la charge de travail **Développement Desktop en C++** quand vous [installez](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="linux"></a>Linux

Cet article suppose que vous utilisez Ubuntu Linux. Les étapes décrites dans cet article ont été testées à l’aide d’Ubuntu 18,04.

Pour créer le pont d’IoT Plug-and-Play sur Linux, installez **GCC**, **Git**, **cmake** et toutes les dépendances requises à l’aide de la commande `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Vérifiez que la version de `cmake` est supérieure à **2.8.12** et que la version de **GCC** est supérieure à **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Générer le pont IoT Plug-and-Play

Accédez au dossier *pnpbridge* dans le répertoire du référentiel.

Pour Windows, exécutez la commande suivante dans une [Invite de commandes développeur pour Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) :

```cmd
cd scripts\windows
build.cmd
```

De même, pour Linux, exécutez la commande suivante :

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Sur Windows, vous pouvez ouvrir la solution générée par la commande cmake dans Visual Studio 2019. Ouvrez le fichier projet *azure_iot_pnp_bridge.sln* dans le répertoire cmake et définissez le projet *pnpbridge_bin* comme projet de démarrage dans la solution. Vous pouvez maintenant générer l’exemple dans Visual Studio et l’exécuter en mode débogage.

### <a name="edit-the-configuration-file"></a>Modifier le fichier de configuration

Apprenez-en davantage sur les fichiers de configuration dans le document [Concepts de pont IoT Plug-and-Play](concepts-iot-pnp-bridge.md).

Ouvrez le fichier *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.json* dans un éditeur de texte.

- Ajoutez la valeur `connection-string` qui est la _chaîne de connexion de l’appareil_ que vous avez noté précédemment.
- Ajoutez la valeur de `symmetric_key` qui est la valeur de clé d’accès partagé de la _chaîne de connexion de l’appareil_.
- Remplacez la valeur `root_interface_model_id` par `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`.

La première section du fichier de *config.JSON* ressemble maintenant à l’extrait de code suivant :

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Exécuter le pont d’IoT Plug-and-Play

Démarrez l’exemple de capteur d’environnement du pont d’IoT Plug-and-Play. Le paramètre correspond au chemin d’accès au fichier `config.json` que vous avez modifié dans la section précédente :

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Le pont génère une sortie qui ressemble à ceci :

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Utilisez les commandes suivantes pour exécuter le pont sur Linux :

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Télécharger les fichiers de modèle

Plus tard, vous utiliserez l’explorateur Azure IoT pour afficher l’appareil quand celui-ci se connecte à votre hub IoT. L’explorateur Azure IoT a besoin d’une copie locale du fichier de modèle qui correspond à l’**ID de modèle** que votre appareil envoie. Le fichier de modèle permet à l’Explorateur IoT d’afficher les données de télémétrie, les propriétés et les commandes que votre appareil implémente.

Pour télécharger les modèles pour l’explorateur Azure IoT :

1. Créez un dossier nommé *models* sur votre ordinateur local.
1. Enregistrez [EnvironmentalSensor.JSON](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) dans le dossier des *modèles* que vous avez créé à l’étape précédente.
1. Si vous ouvrez ce fichier de modèle dans un éditeur de texte, vous pouvez voir que le modèle définit un composant avec `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` comme ID. Il s’agit du même ID de modèle que celui utilisé dans le fichier *config.JSON*.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois le pont démarré, utilisez l’explorateur Azure IoT pour vérifier qu’il fonctionne. Vous pouvez voir les données de télémétrie, les propriétés et les commandes définies dans le modèle `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

* [Qu’est ce qu’un pont IoT Plug-and-Play ?](./concepts-iot-pnp-bridge.md)
* [Créer, déployer et étendre un pont IoT Plug-and-Play](howto-build-deploy-extend-pnp-bridge.md)
* [Pont IoT Plug-and-Play sur GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
