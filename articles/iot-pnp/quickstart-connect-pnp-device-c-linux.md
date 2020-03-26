---
title: Connecter l’exemple de code d’appareil IoT Plug-and-Play en préversion à IoT Hub (Linux) | Microsoft Docs
description: Créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion sur Linux qui se connecte à un IoT Hub. Utilisez l’outil Azure CLI pour afficher les informations envoyées par l’appareil au hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531247"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion sur Linux à IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ce démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play sur Linux, comment le connecter à votre hub IoT et comment utiliser l’outil Azure CLI pour afficher les informations qu’il envoie au hub. L’exemple d’application est écrit en C et est inclus dans l’Azure IoT device SDK pour C. Un développeur de solutions peut utiliser l’outil Azure CLI pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir à afficher de code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide suppose que vous utilisez Ubuntu Linux. Les étapes de ce didacticiel ont été testées à l’aide d’Ubuntu 18.04.

Pour suivre ce démarrage rapide, vous devez installer les logiciels suivants sur votre machine locale Linux :

Installez **GCC**, **Git**, **cmake** et toutes les dépendances à l’aide de la commande `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Vérifiez que la version de `cmake` est supérieure à **2.8.12** et que la version de **GCC** est supérieure à **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer le kit Azure IoT Hub Device C SDK.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT C SDKs and Libraries](https://github.com/Azure/azure-iot-sdk-c) à cet emplacement :

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Attendez-vous à ce que cette opération prenne plusieurs minutes.

## <a name="build-the-code"></a>Générer le code

Vous utilisez le kit de développement logiciel (SDK) de l’appareil pour créer l’exemple de code inclus. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Créez un sous-répertoire `cmake` dans le dossier racine du SDK d’appareil, puis accédez à ce dossier :

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Exécutez les commandes suivantes pour générer le SDK d’appareil et le stub de code généré :

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Mettre à jour votre référentiel de modèles

Avant d’exécuter l’exemple, ajoutez le modèle de fonctionnalité de l'appareil et les définitions d’interface au référentiel de modèles de votre entreprise :

1. Connectez-vous au [portail Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com) avec votre compte professionnel ou scolaire Microsoft, ou votre ID de partenaire Microsoft, le cas échéant.

1. Sélectionnez **Référentiel d’entreprise**, puis **Modèles de capacité**.

1. Sélectionnez **Nouveau**, puis **Télécharger**.

1. Sélectionnez le fichier `SampleDevice.capabilitymodel.json` dans le dossier `digitaltwin_client/samples` dans le dossier racine du kit de développement logiciel (SDK) de l’appareil. Sélectionnez **Ouvrir** puis **Enregistrer** pour télécharger le fichier de modèle dans votre référentiel.

1. Sélectionnez **Référentiel d’entreprise**, puis **Interfaces**.

1. Sélectionnez **Nouveau**, puis **Télécharger**.

1. Sélectionnez le fichier `EnvironmentalSensor.interface.json` dans le dossier `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` dans le dossier racine du kit de développement logiciel (SDK) de l’appareil. Sélectionnez **Ouvrir** puis **Enregistrer** pour télécharger le fichier d’interface dans votre référentiel.

1. Sélectionnez **Référentiel d’entreprise**, puis **Chaînes de connexion**. Prenez note de la première _chaîne de connexion de référentiel de modèle d’entreprise_, car vous en aurez besoin plus loin dans ce guide de démarrage rapide.

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Exécutez un exemple d’application dans le kit de développement logiciel (SDK) pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application :

1. Dans le dossier `cmake`, accédez au dossier qui contient le fichier exécutable :

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Exécutez le fichier exécutable :

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

L’appareil, désormais prêt à recevoir des commandes et des mises à jour de propriétés, a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Utiliser Azure IoT CLI pour valider le code

Une fois l’exemple de client d’appareil démarré, vérifiez qu’il fonctionne avec Azure CLI.

Utilisez la commande suivante pour afficher les données de télémétrie que l’exemple d’appareil envoie. Vous devrez peut-être attendre une minute ou deux avant de voir les données de télémétrie dans la sortie :

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Utilisez la commande suivante pour afficher les propriétés envoyées par l’appareil :

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](howto-develop-solution.md)
