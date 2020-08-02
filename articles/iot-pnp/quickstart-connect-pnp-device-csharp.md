---
title: Connecter le code IoT Plug-and-Play en préversion à IoT Hub - C# | Microsoft Docs
description: À l’aide de C# (.NET), créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion qui se connecte à un IoT Hub. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044188"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion à IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ce démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT et comment utiliser l’outil Azure IoT Explorer pour afficher les informations qu’il envoie au hub. L’exemple d’application est écrit en C# (avec .NET) et est fourni dans le cadre de la collection d’exemples Azure IoT pour C# (.NET). Un développeur de solutions peut utiliser l’outil Azure IoT Explorer pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir à afficher de code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez installer .NET Core 3.0 sur votre ordinateur de développement. Vous pouvez télécharger cette version du kit SDK .NET Core pour plusieurs plateformes à partir de [Télécharger .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Vous pouvez vérifier la version de .NET présente sur votre ordinateur de développement en exécutant la commande suivante dans une fenêtre de terminal local : 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Installer l’explorateur Azure IoT

Téléchargez et installez la dernière version de l’**explorateur Azure IoT** à partir de la page [référentiel](https://github.com/Azure/azure-iot-explorer/releases) de l’outil, en sélectionnant le fichier . msi sous « Assets » pour obtenir la mise à jour la plus récente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer les exemples Azure IoT pour C# (.NET).

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Cette opération peut prendre plusieurs minutes.

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Vous utilisez l’exemple de code cloné pour générer une application simulant un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans une fenêtre de terminal local, accédez au dossier de votre dépôt cloné, puis accédez au dossier **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**. 

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Exécutez un exemple d’application pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Dans la même fenêtre de terminal, pour générer les packages nécessaires et exécuter l’exemple d’application, utilisez la commande suivante :

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Vous voyez des messages indiquant que l’appareil s’est correctement inscrit et attend des mises à jour du cloud. L’appareil est donc prêt à recevoir des commandes et des mises à jour de propriétés et a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Pour vérifier que l’outil peut lire les définitions de modèle d’interface à partir de votre appareil, sélectionnez **Paramètres**. Dans le menu Paramètres, **Sur l’appareil connecté** peut déjà apparaître dans les configurations Plug-and-Play. Si ce n’est pas le cas, sélectionnez **+ Ajouter une source de définition de module**, puis **Sur l’appareil connecté** pour l’ajouter.

1. Dans la page de vue d’ensemble **Appareils**, recherchez l’identité d’appareil que vous avez créée précédemment. L’application de l’appareil étant toujours en cours d’exécution à l’invite de commandes, vérifiez que l’**État de connexion** de l’appareil dans Azure IoT Explorer indique _Connecté_ (dans le cas contraire, appuyez sur **Actualiser** jusqu’à ce que cet état apparaisse). Sélectionnez l’appareil pour afficher plus de détails.

1. Développez l’interface avec l’ID **urn:csharp_sdk_sample:EnvironmentalSensor:1** pour révéler l’interface et les primitives IoT Plug-and-Play (propriétés, commandes et données de télémétrie).

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil IoT Plug-and-Play (préversion) et interagir avec celui-ci](howto-develop-solution.md)
