---
title: Provisionner des appareils Windows pour la supervision à distance en C - Azure | Microsoft Docs
description: Explique comment connecter un appareil à l’accélérateur de solution de supervision à distance avec une application écrite en C exécutée sur Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450214"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Connecter votre appareil à l’accélérateur de solution de supervision à distance (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce tutoriel vous montre comment connecter un appareil réel à l’accélérateur de solution de supervision à distance.

Comme avec la plupart des applications embarquées qui s’exécutent sur des appareils limités, le code client pour l’application d’appareil est écrit en C. Dans ce didacticiel, vous générez l’application cliente d’appareil sur une machine exécutant Windows.

Si vous préférez simuler un appareil, consultez [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes décrites dans ce guide pratique, suivez les étapes de [Configurer votre environnement de développement Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) pour ajouter les outils et bibliothèques de développement requis pour votre machine Windows.

## <a name="view-the-code"></a>Afficher le code

L’[exemple de code](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilisé dans ce guide est disponible dans le référentiel GitHub des kits de développement logiciel (SDK) Azure IoT C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Télécharger le code source et préparer le projet

Pour préparer le projet, [clonez le dépôt de SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) à partir de GitHub.

L’exemple se trouve dans le dossier **samples/solutions/remote_monitoring_client**.

Ouvrez le fichier **remote_monitoring.c** dans le dossier **samples/solutions/remote_monitoring_client** dans un éditeur de texte.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Dans le fichier **remote_monitoring.c**, remplacez `<connectionstring>` par la chaîne de connexion d’appareil que vous avez notée au début de ce guide pratique lorsque vous avez ajouté un appareil à l’accélérateur de solution.

1. Suivez les étapes de [Build the C SDK in Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) (générer le SDK C dans Windows) pour générer le kit de développement logiciel et l’application cliente de surveillance à distance.

1. Sur l’invite de commandes qui vous a permis de générer la solution, exécutez :

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    La console affiche des messages quand l’application :

    - L’application envoie un exemple de données de télémétrie à l’accélérateur de solution.
    - Répond aux méthodes appelées à partir du tableau de bord des solutions.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
