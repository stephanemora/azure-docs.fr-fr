---
title: Connecter l’exemple de code d’appareil IoT Plug-and-Play en préversion à IoT Hub (Linux) | Microsoft Docs
description: Créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion sur Linux qui se connecte à un IoT Hub. Utilisez l’outil Azure CLI pour afficher les informations envoyées par l’appareil au hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585975"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion sur Linux à IoT Hub

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

## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

Si vous utilisez Azure CLI localement, la version de `az` doit être **2.0.73** ou une version ultérieure ; Azure Cloud Shell utilise la version la plus récente. Utilisez la commande `az --version` pour vérifier la version installée sur votre ordinateur.

Ajoutez l’extension Microsoft Azure IoT pour Azure CLI :

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Les étapes de ce guide de démarrage rapide nécessitent la version **0.8.5** ou une version ultérieure de l’extension. Utilisez la commande `az extension list` pour vérifier la version que vous avez installée, et la commande `az extension update` pour mettre à jour si nécessaire.

Si vous utilisez l’interface CLI localement, connectez-vous à votre abonnement Azure avec la commande suivante :

```bash
az login
```

Si vous utilisez Azure Cloud Shell, vous êtes déjà connecté automatiquement.

Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../iot-hub/iot-hub-create-using-cli.md). Au cours de la préversion publique, IoT Plug-and-Play est disponible dans les régions Europe Nord, USA Centre et Japon Est. Veillez à créer votre hub dans l’une de ces régions.

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez l’espace réservé **YourIoTHubName** par le nom réel de votre hub IoT :

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion_ de l’appareil que vous venez d’inscrire :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce démarrage rapide, vous préparez un environnement de développement pour cloner et générer le Azure IoT device SDK pour C.

Ouvrez une invite de commandes. Exécutez la commande suivante pour cloner le référentiel GitHub du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) :

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Cette commande prend plusieurs minutes à s’exécuter.

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

1. Sélectionnez **Référentiel d’entreprise**, puis **Chaînes de connexion**. Prenez note de la première chaîne de connexion de référentiel de modèle d’entreprise, vous l’utiliserez plus loin dans ce guide de démarrage rapide.

## <a name="run-the-sample"></a>Exécution de l'exemple

Exécutez un exemple d’application dans le kit de développement logiciel (SDK) pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application :

1. Dans le dossier `cmake`, accédez au dossier qui contient le fichier exécutable :

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Exécutez le fichier exécutable :

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

L’appareil simulé commence à envoyer des données de télémétrie, à écouter les commandes et à écouter les mises à jour de propriétés.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Utiliser Azure IoT CLI pour valider le code

Une fois l’exemple de client d’appareil démarré, vérifiez qu’il fonctionne avec Azure CLI.

Utilisez la commande suivante pour afficher les données de télémétrie que l’exemple d’appareil envoie. Vous devrez peut-être attendre une minute ou deux avant de voir les données de télémétrie dans la sortie :

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

Utilisez la commande suivante pour afficher les propriétés envoyées par l’appareil :

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](howto-develop-solution.md)
