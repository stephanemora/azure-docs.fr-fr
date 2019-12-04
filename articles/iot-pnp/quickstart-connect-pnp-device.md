---
title: Connecter l’exemple de code pour appareil IoT Plug-and-Play en préversion à IoT Hub (Windows) | Microsoft Docs
description: Créez et exécutez l’exemple de code pour appareil IoT Plug-and-Play en préversion sur Windows qui se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152074"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion sur Windows à IoT Hub (C Windows)

Ce démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT et comment utiliser l’outil Azure IoT Explorer pour afficher les informations qu’il envoie au hub. L’exemple d’application est écrit en C et est inclus dans le kit Azure IoT Hub Device C SDK. Un développeur de solutions peut utiliser l’outil Azure IoT Explorer pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir à afficher de code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vous devez installer les logiciels suivants sur votre machine locale :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : assurez-vous d’inclure le composant **Gestionnaire de package NuGet** et la charge de travail **Développement Desktop en C++** lorsque vous installez Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installer l’explorateur Azure IoT

Téléchargez et installez la dernière version de l’**explorateur Azure IoT** à partir de la page [référentiel](https://github.com/Azure/azure-iot-explorer/releases) de l’outil, en sélectionnant le fichier . msi sous « Assets » pour obtenir la mise à jour la plus récente.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer le kit Azure IoT Hub Device C SDK.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT C SDKs and Libraries](https://github.com/Azure/azure-iot-sdk-c) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Attendez-vous à ce que cette opération prenne plusieurs minutes.

## <a name="build-the-code"></a>Générer le code

Vous utilisez le kit de développement logiciel (SDK) de l’appareil pour créer l’exemple de code inclus. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Créez un sous-répertoire `cmake` dans le dossier racine du SDK d’appareil, puis accédez à ce dossier :

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Exécutez les commandes suivantes pour générer le SDK d’appareil et le stub de code généré :

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Si cmake ne parvient pas à trouver votre compilateur C++, vous obtenez des erreurs de build lorsque vous exécutez la commande précédente. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Exécutez un exemple d’application dans le kit de développement logiciel (SDK) pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application, utilisez ces commandes et passez la _chaîne de connexion d’appareil_ en tant que paramètre.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

L’appareil, désormais prêt à recevoir des commandes et des mises à jour de propriétés, a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

1. Ouvrez l’explorateur Azure IoT. La page **Configurations de l’application** apparaît.

1. Entrez votre _chaîne de connexion IoT Hub_, puis sélectionnez **Se connecter**.

1. Une fois que vous êtes connecté, la page de vue d’ensemble **Appareils** s’affiche.

1. Pour vérifier que l’outil peut lire les définitions de modèle d’interface à partir de votre appareil, sélectionnez **Paramètres**. Dans le menu Paramètres, **Sur l’appareil connecté** peut déjà apparaître dans les configurations Plug-and-Play. Si ce n’est pas le cas, sélectionnez **+ Ajouter une source de définition de module**, puis **Sur l’appareil connecté** pour l’ajouter.

1. Dans la page de vue d’ensemble **Appareils**, recherchez l’identité d’appareil que vous avez créée précédemment. L’application de l’appareil étant toujours en cours d’exécution à l’invite de commandes, vérifiez que l’**État de connexion** de l’appareil dans Azure IoT Explorer indique _Connecté_ (dans le cas contraire, appuyez sur **Actualiser** jusqu’à ce que cet état apparaisse). Sélectionnez l’appareil pour afficher plus de détails.

1. Développez l’interface avec l’ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** pour révéler l’interface et les primitives IoT Plug-and-Play (propriétés, commandes et données de télémétrie).

1. Sélectionnez la page **Télémétrie** et appuyez sur _Démarrer_ pour afficher les données de télémétrie envoyées par l’appareil.

1. Sélectionnez la page **Properties(non-writable)** (Propriétés (non accessibles en écriture)) pour afficher les propriétés non accessibles en écriture signalées par l’appareil.

1. Sélectionnez la page **Properties(writable)** (Propriétés (accessibles en écriture)) pour afficher les propriétés accessibles en écriture que vous pouvez mettre à jour.

1. Développez le **nom** de la propriété, mettez-le à jour avec un nouveau nom et sélectionnez **Update writable property** (Mettre à jour la propriété accessible en écriture). 

1. Pour voir le nouveau nom s’afficher dans la colonne **Propriété rapportée**, cliquez sur le bouton **Actualiser** en haut de la page.

1. Sélectionnez la page **Commandes** pour afficher toutes les commandes prises en charge par l’appareil.

1. Développez la commande **blink** et définissez un nouvel intervalle de clignotement. Sélectionnez **Envoyer la commande** pour appeler la commande sur l’appareil.

1. Accédez à l’invite de commandes de l’appareil simulé et lisez les messages de confirmation imprimés pour vérifier que les commandes sont exécutées comme prévu.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil IoT Plug-and-Play (préversion) et interagir avec celui-ci](howto-develop-solution.md)
