---
title: Tutoriel - Connecter un appareil IoT Plug-and-Play (préversion) à Azure IoT Central
description: Ce tutoriel vous montre comment utiliser un modèle de fonctionnalité d’appareil pour générer du code d’appareil. Ensuite, exécutez le code de l’appareil, examinez la connexion d’appareil à votre application IoT Central et utilisez les vues générées automatiquement.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 233d8f345eceb188fcc849457c5243a3c64aaec2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75548851"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Tutoriel : Utiliser un modèle de fonctionnalité d’appareil mobile pour créer un appareil IoT Plug-and-Play et le connecter à votre application IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Un _modèle de fonctionnalité d’appareil_ décrit les fonctionnalités d’un appareil [IoT Plug-and-Play](../../iot-pnp/overview-iot-plug-and-play.md). IoT Central peut utiliser un modèle de fonctionnalité d’appareil pour créer un modèle d’appareil et des visualisations pour un appareil quand l’appareil se connecte pour la première fois.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Utilisez Visual Studio Code pour créer un appareil IoT Plug-and-Play à l’aide d’un modèle de fonctionnalité d’appareil.
> * Exécutez le code de l’appareil dans Windows et vérifiez qu’il se connecte à votre application IoT Central.
> * Affichez la télémétrie simulée que l’appareil envoie.

## <a name="prerequisites"></a>Conditions préalables requises

Suivez le guide de démarrage rapide [Créer une application Azure IoT Central (fonctionnalités en préversion)](./quick-deploy-iot-central.md) pour créer une application IoT Central à l’aide du modèle **Custom app > Preview application** (Application personnalisée > Application en préversion).

Pour suivre ce tutoriel, vous devez installer les logiciels suivants sur votre ordinateur local :

* [Build Tools pour Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) avec les **outils de génération C++** et les charges de travail du **composant Gestionnaire de package Nuget**. Ou, si vous l'avez déjà, [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 avec les mêmes charges de travail.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) : quand vous installez **CMake**, sélectionnez l’option **Add CMake to the system PATH**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.JS](https://nodejs.org/)
* L’utilitaire `dps-keygen` :

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Installer Azure IoT Tools

Pour installer le pack d’extension Azure IoT Tools dans VS Code, effectuez les étapes suivantes :

1. Dans VS Code, sélectionnez l’onglet **Extensions**.
1. Recherchez **Azure IoT Tools**.
1. Sélectionnez **Installer**.

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce tutoriel, vous utilisez le gestionnaire de bibliothèques [Vcpkg](https://github.com/microsoft/vcpkg) pour installer le kit Azure IoT device SDK pour C dans votre environnement de développement.

1. Ouvrez une invite de commandes. Exécutez la commande suivante pour installer Vcpkg :

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ensuite, pour raccorder [l’intégration](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) à l’utilisateur, exécutez la commande suivante. La première fois que vous exécutez cette commande, elle nécessite des droits d’administration :

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installez le Vcpkg du kit de développement logiciel (SDK) C de l’appareil Azure IoT :

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Générer la clé de l’appareil

Pour connecter un appareil à une application IoT Central, vous avez besoin d’une clé d’appareil. Pour générer une clé d’appareil :

1. Connectez-vous à l’application IoT Central que vous avez créée à partir du modèle **Custom app > Preview application** (Application personnalisée > Application en préversion) dans le guide de démarrage rapide [Créer une application Azure IoT Central (fonctionnalités en préversion)](./quick-deploy-iot-central.md).

1. Accédez à la page **Administration** et sélectionnez **Connexion de l’appareil**.

1. Notez les valeurs des champs **ID Score** (Étendue de l’ID) et **Primary Key** (Clé primaire) qui s’affichent quand vous sélectionnez **View Keys** (Afficher les clés). Vous utiliserez ces valeurs plus tard dans ce tutoriel.

    ![Connexion de l’appareil](./media/tutorial-connect-pnp-device/device-connection.png)

1. Ouvrez une invite de commandes et exécutez la commande suivante pour générer une clé d’appareil :

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Prenez note de la _clé d’appareil_ générée. Vous utiliserez cette valeur dans une étape ultérieure de ce tutoriel.

## <a name="download-your-model"></a>Télécharger votre modèle

Dans ce tutoriel, vous allez utiliser le modèle de fonctionnalité d’appareil public pour un appareil DevKit IoT MxChip. Vous n’avez pas besoin d’un appareil DevKit réel pour exécuter le code. Dans ce tutoriel, vous allez compiler le code à exécuter sur Windows.

1. Créez un dossier nommé `central_app` et ouvrez-le dans VS Code.

1. Utilisez **Ctrl + Maj + P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play**, puis sélectionnez **Open Model Repository (Ouvrir le référentiel de modèles)** . Sélectionnez **Public repository (Référentiel public)** . VS Code affiche une liste des modèles de fonctionnalités d’appareils dans le référentiel de modèles public.

1. Sélectionnez le modèle de fonctionnalité d’appareil **MXChip IoT DevKit** avec l’ID `urn:mxchip:mxchip_iot_devkit:1`. Ensuite, sélectionnez **Télécharger**. Vous disposez maintenant d’une copie du modèle de fonctionnalité d’appareil dans le dossier `central_app`.

![Référentiel de modèles et modèle de fonctionnalité d’appareil](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Pour que le modèle de capacité d’appareil fonctionne avec IoT Central, toutes les interfaces correspondantes doivent être définies inline dans le même fichier.

## <a name="generate-the-c-code-stub"></a>Générer le stub de code C

Maintenant que vous avez le modèle de fonctionnalité d’appareil **MXChip IoT DevKit** et ses interfaces associées, vous pouvez générer le code de l’appareil qui implémente le modèle. Pour générer le stub de code C dans VS Code :

1. Ouvrez le dossier contenant les fichiers DCM, appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play**, puis sélectionnez **Générer le stub de code de l’appareil**.

    > [!NOTE]
    > La première fois que vous utilisez l’outil de génération de code IoT Plug-and-Play, le téléchargement prend quelques secondes.

1. Sélectionnez le fichier de modèle de fonctionnalité d’appareil **MXChip IoT DevKit** que vous venez de télécharger.

1. Entrez le nom du projet **devkit_device**.

1. Choisissez **ANSI C** comme langage.

1. Choisissez la méthode de connexion **Via DPS (Device Provisioning Service) symmetric key (Avec une clé symétrique DPS (service Device Provisioning))** .

1. Choisissez **Projet CMake sur Windows** comme type de projet. Ne choisissez pas **Projet MXChip IoT DevKit**. Cette option est prévue pour les appareils DevKit réel.

1. Choisissez **Via Vcpkg** pour inclure le kit SDK.

1. VS Code ouvre une nouvelle fenêtre avec les fichiers de stub de code d’appareil générés dans le dossier `devkit_device`.

![Code d’appareil généré](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Générer le code

Vous utilisez le SDK d’appareil pour générer le stub de code d’appareil généré. L’application que vous créez simule un appareil **MXChip IoT DevKit** et se connecte à votre application IoT central. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Depuis une invite de commandes, créez un sous-répertoire `cmake` dans le dossier `devkit_device`, puis accédez à ce dossier :

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Exécutez les commandes suivantes pour générer le stub de code généré. Remplacez l’espace réservé `<directory of your Vcpkg repo>` par le chemin de votre copie du dépôt **Vcpkg** :

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Si vous utilisez Visual Studio 2017 ou 2015, vous devez spécifier le générateur CMake en fonction des outils de génération employés :

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Une fois la génération terminée, dans la même invite de commandes, exécutez votre application. Remplacez `<scopeid>` et `<primarykey>` par les valeurs que vous avez notées :

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. L’application de l’appareil commence à envoyer des données à IoT Hub. Parfois, vous voyez l’erreur `Error registering device for DPS` la première fois que vous exécutez la commande précédente. Si vous voyez cette erreur, réexécutez la commande.

## <a name="view-the-device"></a>Afficher l’appareil

Une fois que le code de votre appareil se connecte à votre application IoT Central, vous pouvez afficher les propriétés et télémétrie qu’il envoie :

1. Dans votre application IoT Central, accédez à la page **Appareils**, puis sélectionnez l’appareil **mxchip-01**. Cet appareil a été automatiquement ajouté quand le code de l’appareil s’est connecté :

    ![Page Vue d’ensemble](./media/tutorial-connect-pnp-device/overview-page.png)

    Après quelques minutes, cette page affiche les graphiques des données de télémétrie que l’appareil envoie.

1. Sélectionnez la page **À propos de** pour afficher les valeurs de propriété que l’appareil a envoyées.

1. Sélectionnez la page **Commandes** pour appeler des commandes sur l’appareil. Vous pouvez voir que l’appareil répond à l’invite de commandes qui exécute le code de l’appareil.

1. Accédez à la page **Modèles d’appareil** pour voir le modèle que IoT central a créé à partir du modèle de fonctionnalité d’appareil dans le référentiel public :

    ![Page Modèles d’appareil](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à connecter un appareil IoT Plug-and-Play qui a été généré à partir d’un modèle de fonctionnalité d’appareil du dépôt de modèles public.

Pour en savoir plus sur les modèles de fonctionnalités d’appareils et sur la façon de créer vos propres modèles, poursuivez avec le guide pratique :

> [!div class="nextstepaction"]
> [Créer un groupe d’appareils](./tutorial-use-device-groups.md)
