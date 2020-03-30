---
title: Approvisionner des appareils Linux pour la surveillance à distance en C - Azure| Microsoft Docs
description: Explique comment connecter un appareil à l’accélérateur de solution de supervision à distance avec une application écrite en C et exécutée sur Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454490"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Connecter votre appareil à l’accélérateur de solution de supervision à distance (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce tutoriel vous montre comment connecter un appareil réel à l’accélérateur de solution de supervision à distance.

Comme avec la plupart des applications incorporées qui s’exécutent sur des appareils limités, le code client pour l’application d’appareil est écrit en C. Dans ce didacticiel, vous générez l’application sur une machine exécutant Ubuntu (Linux).

Si vous préférez simuler un appareil, consultez [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes dans ce guide pratique, votre appareil doit exécuter Ubuntu 15.04 ou version ultérieure. Avant toute chose, [configurez votre environnement de développement Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>Afficher le code

L’[exemple de code](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilisé dans ce guide est disponible dans le référentiel GitHub des kits de développement logiciel (SDK) Azure IoT C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Télécharger le code source et préparer le projet

Pour préparer le projet, clonez ou téléchargez le [référentiel de kits de développement logiciel (SDK) Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) à partir de GitHub.

L’exemple se trouve dans le dossier **samples/solutions/remote_monitoring_client**.

Ouvrez le fichier **remote_monitoring.c** dans le dossier **samples/solutions/remote_monitoring_client** dans un éditeur de texte.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

Les étapes suivantes décrivent comment utiliser *CMake* pour générer votre application cliente. L’application cliente de supervision à distance est générée dans le cadre du processus de génération du kit de développement logiciel (SDK).

1. Dans le fichier **remote_monitoring.c**, remplacez `<connectionstring>` par la chaîne de connexion d’appareil que vous avez notée au début de ce guide pratique lorsque vous avez ajouté un appareil à l’accélérateur de solution.

1. Accédez à la racine de votre copie clonée du [référentiel de kits de développement logiciel (SDK) Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) et exécutez les commandes suivantes pour générer l’application cliente :

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Exécutez l’application cliente et envoyez les données de télémétrie à IoT Hub :

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    La console affiche des messages quand l’application :

    - L’application envoie un exemple de données de télémétrie à l’accélérateur de solution.
    - Répond aux méthodes appelées à partir du tableau de bord des solutions.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
