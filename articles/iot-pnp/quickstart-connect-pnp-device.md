---
title: Connecter l’exemple de code d’appareil IoT Plug-and-Play en préversion à IoT Hub | Microsoft Docs
description: Créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion qui se connecte à un IoT Hub. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4802c120f4722796fcad4ea99f25adad368451fd
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806538"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion à IoT Hub

Ce démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT et comment utiliser l’outil Azure IoT Explorer pour afficher les informations qu’il envoie au hub. L’exemple d’application est écrit en C et est inclus dans le Azure IoT device SDK pour C. Un développeur de solutions peut utiliser l’outil Azure IoT Explorer pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir à afficher de code d’appareil.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vous devez installer les logiciels suivants sur votre machine locale :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : assurez-vous d’inclure le composant **Gestionnaire de package NuGet** et la charge de travail **Développement Desktop en C++** lorsque vous installez Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installer l’explorateur Azure IoT

Téléchargez et installez l’outil Explorateur Azure IoT à partir de la page sur la [version la plus récente](https://github.com/Azure/azure-iot-explorer/releases).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

Ajoutez l’extension Microsoft Azure IoT pour Azure CLI :

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez les espaces réservés **YourIoTHubName** et **YourDevice** par vos noms réels. Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../iot-hub/iot-hub-create-using-cli.md) :

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion_ de l’appareil que vous venez d’inscrire :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub :

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

### <a name="get-azure-iot-device-sdk-for-c"></a>Se procurer Azure IoT device SDK pour C

Dans ce démarrage rapide, vous préparez un environnement de développement pour cloner et générer le Azure IoT device SDK pour C.

Ouvrez une invite de commandes. Exécutez la commande suivante pour cloner le référentiel GitHub du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Attendez-vous à ce que cette opération prenne plusieurs minutes.

## <a name="build-the-code"></a>Générer le code

L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

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

Exécutez votre application en transmettant la chaîne de connexion de l’appareil IoT Hub en tant que paramètre.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

L’application de l’appareil commence à envoyer des données à IoT Hub.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

1. Ouvrez l’Explorateur Azure IoT pour ouvrir la page **Configurations de l’application**.

1. Entrez votre chaîne de connexion IoT Hub, puis cliquez sur **Se connecter**.

1. Une fois que vous êtes connecté, la page de vue d’ensemble de l’appareil s’affiche.

1. Pour ajouter le référentiel de votre entreprise, sélectionnez **Paramètres**, puis **+ Nouveau**, puis **Sur l’appareil connecté**.

1. Sur la page de vue d’ensemble de l’appareil, recherchez l’identité d’appareil que vous avez créée précédemment et sélectionnez-la pour afficher plus de détails.

1. Développez l’interface avec l’ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** pour voir les primitives IoT Plug-and-Play (propriétés, commandes et télémétrie).

1. Sélectionnez la page **Télémétrie** pour afficher les données de télémétrie envoyées par l’appareil.

1. Sélectionnez la page **Properties(non-writable)** (Propriétés (non accessibles en écriture)) pour afficher les propriétés non accessibles en écriture signalées par l’appareil.

1. Sélectionnez la page **Properties(writable)** (Propriétés (accessibles en écriture)) pour afficher les propriétés accessibles en écriture que vous pouvez mettre à jour.

1. Développez le **nom** de la propriété, mettez-le à jour avec un nouveau nom et sélectionnez **Update writable property (Mettre à jour la propriété accessible en écriture)** . 

1. Pour voir le nouveau nom s’afficher dans la colonne **Propriété rapportée**, cliquez sur le bouton **Actualiser** en haut de la page.

1. Sélectionnez la page **Commande** pour afficher toutes les commandes prises en charge par l’appareil.

1. Développez la commande **blink** et définissez un nouvel intervalle de clignotement. Sélectionnez **Envoyer la commande** pour appeler la commande sur l’appareil.

1. Accédez à l’appareil simulé pour vérifier que la commande s’est exécutée comme prévu.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil IoT Plug-and-Play (préversion) et interagir avec celui-ci](howto-develop-solution.md)
