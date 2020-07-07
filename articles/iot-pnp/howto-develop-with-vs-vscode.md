---
title: Utiliser Visual Studio et Visual Studio Code pour générer des appareils IoT Plug-and-Play en préversion | Microsoft Docs
description: Utilisez Visual Studio et Visual Studio Code pour accélérer la création de modèles d’appareil IoT Plug-and-Play et l’implémentation du code d’appareil.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 038d9ff39f388d1ef7b09b951c09dbe3420858b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84298225"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Utiliser Visual Studio et Visual Studio Code pour générer des appareils IoT Plug-and-Play

Azure IoT Tools pour Visual Studio Code fournit un environnement intégré pour créer des interfaces et des modèles de capacité d’appareil (DCM, device capability model), publier dans des référentiels de modèles et générer le code de squelette en C pour implémenter l’application d’appareil.

Cet article vous montre comment :

- Générer le code d’appareil et le projet d’application
- Utiliser le code généré dans votre projet d’appareil
- Itérer en régénérant le code de squelette

Pour en savoir plus sur l’utilisation de VS Code pour développer des appareils IoT, consultez [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Prérequis

Installez [Visual Studio Code](https://code.visualstudio.com/).

Effectuez les étapes suivantes pour installer le pack d’extension dans VS Code.

1. Dans VS Code, sélectionnez l’onglet **Extensions**.
1. Recherchez et installez **Azure IoT Tools** à partir de la Place de marché.

## <a name="generate-device-code-and-project"></a>Générer le code d’appareil et le projet

Dans VS Code, appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play**, puis sélectionnez **Générer le stub de code de l’appareil** pour configurer le code de squelette et le type de projet. La liste ci-dessous décrit chaque étape en détail :

- **Fichier DCM à utiliser pour générer le code**. Pour générer le code de squelette de l’appareil, ouvrez le dossier qui contient les fichiers DCM et d’interface qu’il implémente. Si le générateur de code ne trouve pas une interface requise par un DCM, il la télécharge à partir du référentiel de modèles si nécessaire.

- **Langage du code d’appareil**. Actuellement, le générateur de code prend en charge uniquement le langage C ANSI.

- **Nom du projet**. Le nom du projet est utilisé comme nom de dossier pour le code généré et d’autres fichiers projet. Par défaut, le dossier est placé en regard du fichier DCM. Pour éviter d’avoir à copier manuellement le dossier du code généré chaque fois que vous mettez à jour le DCM et régénérez le code d’appareil, conservez votre fichier DCM dans le dossier du projet.

- **Méthode de connexion à Azure IoT**. Les fichiers générés contiennent également du code permettant de configurer l’appareil pour qu’il se connecte à IoT Hub. Vous pouvez choisir de le connecter directement à [IoT Hub](https://docs.microsoft.com/azure/iot-hub) ou d’utiliser le [service Device Provisioning](https://docs.microsoft.com/azure/iot-dps).

    - **Avec une chaîne de connexion d’appareil IoT Hub** : spécifiez la chaîne de connexion de l’appareil pour que l’application d’appareil se connecte directement à IoT Hub.
    - **Avec une clé symétrique DPS** : spécifiez l’**étendue de l’ID**, la **clé symétrique** et l’**ID de l’appareil** pour l’application d’appareil, nécessaires pour la connexion à IoT Hub ou à IoT Central avec le service DPS.

- **Type de projet**. Le générateur de code génère également un projet CMake ou Arduino. Actuellement, les types de projet pris en charge sont :

    - **Projet CMake sur Windows** : pour un projet d’appareil qui utilise [CMake](https://cmake.org/) comme système de génération sur Windows. Cette option génère `CMakeLists.txt` avec les configurations du kit de développement logiciel (SDK) de l’appareil dans le même dossier que le code C.
    - **Projet CMake sur Linux** : pour un projet d’appareil qui utilise [CMake](https://cmake.org/) comme système de génération sur Linux. Cette option génère `CMakeLists.txt` avec les configurations du kit de développement logiciel (SDK) de l’appareil dans le même dossier que le code C.
    - **Projet DevKit IoT MXChip** : pour un projet d’appareil exécuté sur un appareil [DevKit IoT MXChip](https://aka.ms/iot-devkit). Cette option génère un projet Arduino que vous pouvez [utiliser dans VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) ou dans l’IDE Arduino pour générer et exécuter le projet sur un appareil DevKit IoT.

- **Type de SDK d’appareil**. Si vous sélectionnez CMake comme type de projet, cette étape permet de configurer la façon dont le code généré inclut Azure IoT C device SDK dans `CMakeLists.txt` :

    - **Via le code source** : le code généré s’appuie sur le [code source du kit de développement logiciel (SDK) de l’appareil](https://github.com/Azure/azure-iot-sdk-c) pour l’inclure et le créer conjointement. Cette option est recommandée lorsque vous avez personnalisé le code source du kit de développement logiciel (SDK) de l’appareil.
    - **Via le Vcpkg** : le code généré s’appuie sur le [Vcpkg du kit de développement logiciel (SDK) de l’appareil](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) pour l’inclure et le créer conjointement. Il s’agit de la méthode recommandée pour les appareils exécutant Windows, Linux ou macOS.

    > [!NOTE]
    > La prise en charge sur macOS du Vcpkg pour le kit de développement logiciel (SDK) C de l’appareil Azure IoT C est prévue pour plus tard.

Le générateur de code tente d’utiliser les fichiers DCM et d’interface situés dans le dossier local. Si les fichiers d’interface ne se trouvent pas dans le dossier local, le générateur de code les recherche dans le référentiel de modèles public ou le référentiel de modèles d’entreprise. Les [fichiers de l’interface commune](./concepts-common-interfaces.md) sont stockés dans le référentiel de modèles public.

Une fois la génération du code terminée, l’extension ouvre une nouvelle fenêtre VS Code contenant le code. Si vous ouvrez un fichier généré tel que **main.c**, il se peut qu’IntelliSense indique qu’il ne peut pas ouvrir les fichiers sources du SDK C. Pour permettre à IntelliSense de naviguer correctement dans le code, incluez la source du SDK C en procédant comme suit :

1. Dans VS Code, appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes. Tapez et sélectionnez **C/C++: Edit Configurations (JSON)** pour ouvrir le fichier **c_cpp_properties.json**.

1. Ajoutez le chemin du SDK de l’appareil dans la section `includePath` :

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Enregistrez le fichier .

## <a name="use-the-generated-code"></a>Utiliser le code généré

Les instructions suivantes décrivent comment utiliser le code généré dans votre propre projet d’appareil sur différentes plateformes d’ordinateurs de développement. Elles supposent que vous utilisez une chaîne de connexion d’appareil IoT Hub avec le code généré :

### <a name="linux"></a>Linux

Pour compiler le code d’appareil avec le SDK C Vcpkg de l’appareil en utilisant CMake dans un environnement Linux comme Ubuntu ou Debian :

1. Ouvrez une application de terminal.

1. Installez **GCC**, **Git**, `cmake` et toutes les dépendances à l’aide de la commande `apt-get` :

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Vérifiez que la version de `cmake` est supérieure à **2.8.12** et que la version de **GCC** est supérieure à **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Installez Vcpkg :

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Ensuite, pour raccorder [l’intégration](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) à l’utilisateur, exécutez :

    ```bash
    ./vcpkg integrate install
    ```

1. Installez le Vcpkg du kit de développement logiciel (SDK) C de l’appareil Azure IoT :

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Créez un sous-répertoire `cmake` dans le dossier qui contient le stub de code généré, puis accédez à ce dossier :

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Exécutez les commandes suivantes pour utiliser CMake pour compiler le SDK d’appareil et le stub de code généré :

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Une fois la compilation, exécutez l’application en spécifiant la chaîne de connexion d’appareil IoT Hub comme paramètre.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Pour compiler le code d’appareil avec le SDK C de l’appareil sous Windows en utilisant CMake et les compilateurs Visual Studio C/C++ au niveau de la ligne de commande, consultez le [guide de démarrage rapide sur IoT Plug-and-Play](./quickstart-create-pnp-device-windows.md). Les étapes suivantes vous montrent comment compiler le code d’appareil avec le SDK C Vcpkg de l’appareil en tant que projet CMake dans Visual Studio.

1. Suivez les étapes décrites dans le [démarrage rapide](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) pour installer l’Azure IoT device SDK pour C via Vcpkg.

1. Installez [Visual Studio 2019 (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/). Veillez à inclure le composant **Gestionnaire de package NuGet** et la charge de travail **Développement Desktop en C++** .

1. Ouvrez Visual Studio, choisissez **Fichier > Ouvrir > CMake...** pour ouvrir le fichier `CMakeLists.txt` dans le dossier qui contient le code généré.

1. Dans la barre d’outils **Général**, recherchez la liste déroulante **Configurations**. Sélectionnez **Gérer les configurations** pour ajouter le paramètre CMake pour votre projet.

    ![Gérer la configuration](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Sous **Paramètres CMake**, ajoutez une nouvelle configuration et sélectionnez la cible **x86-Debug**.

1. Sous **Arguments de commande CMake**, ajoutez la ligne suivante :

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Enregistrez le fichier .

1. Basculez vers **x86-Debug** dans la liste déroulante **Configurations**. Il faut quelques secondes pour que le CMake génère son cache. Affichez la fenêtre de sortie pour voir la progression.

    ![Sortie CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le fichier `CMakeLists.txt` dans le dossier racine, puis sélectionnez **Générer** dans le menu contextuel pour compiler le stub de code généré avec le SDK de l’appareil.

1. Une fois la compilation réussie, à l’invite de commandes, exécutez l’application en spécifiant la chaîne de connexion d’appareil IoT Hub en tant que paramètre.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Pour en savoir plus sur l’utilisation de CMake dans Visual Studio, consultez [Génération de projets CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects).

### <a name="macos"></a>macOS

Les étapes suivantes vous montrent comment compiler le code d’appareil avec le code source C du SDK de l’appareil sur macOS en utilisant CMake :

1. Ouvrez une application de terminal.

1. Utilisez [Homebrew](https://brew.sh) pour installer toutes les dépendances :

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Vérifiez que la version de [CMake](https://cmake.org/) est **2.8.12** ou ultérieure :

    ```bash
    cmake --version
    ```

1. [Mettez à jour CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) vers la version la plus récente disponible.

1. Dans le dossier qui contient le code généré, clonez le référentiel du [Kit de développement logiciel (SDK) C Azure IoT](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Attendez-vous à ce que cette opération prenne plusieurs minutes.

1. Créez un dossier appelé `cmake` sous le dossier qui contient le code généré, puis accédez à ce dossier.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Exécutez les commandes suivantes pour utiliser CMake pour compiler le SDK d’appareil et le stub de code généré :

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Une fois la compilation, exécutez l’application en spécifiant la chaîne de connexion d’appareil IoT Hub comme paramètre.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Itérer en régénérant le code de squelette

Le générateur de code peut régénérer le code si vous mettez à jour vos fichiers d’interface ou DCM. En supposant que vous avez déjà généré votre code d’appareil à partir d’un fichier DCM, procédez comme suit pour régénérer le code :

1. Ouvrez le dossier contenant les fichiers DCM, appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play**, puis sélectionnez **Générer le stub de code de l’appareil**.

1. Choisissez le fichier DCM que vous avez mis à jour.

1. Sélectionnez **Régénérer le code pour {nom du projet}** .

1. Le générateur de code utilise les paramètres que vous avez configurés précédemment et régénère le code. Toutefois, il ne remplace pas les fichiers qui peuvent contenir du code utilisateur tels que `main.c` et `{project_name}_impl.c`.

> [!NOTE]
> Si vous mettez à jour l’ID d’URN dans votre fichier d’interface, l’interface est considérée comme une nouvelle interface. Quand vous régénérez le code, le générateur de code génère le code pour l’interface, mais ne remplace pas le code d’origine dans le fichier `{project_name}_impl.c`.

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Azure IoT Tools est un projet open source sur GitHub. Si vous rencontrez des problèmes ou souhaitez formuler des requêtes liées aux fonctionnalités, vous pouvez [créer un problème dans GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article de procédure, vous avez appris à utiliser Visual Studio et Visual Studio Code pour générer le code de squelette en C afin d’implémenter l’application d’appareil. À présent, nous vous invitons à découvrir comment [installer et à utiliser l’explorateur Azure IoT](./howto-install-iot-explorer.md).
