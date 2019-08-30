---
title: Connecter un appareil IoT Plug-and-Play à IoT Central | Microsoft Docs
description: Découvrez comment utiliser Visual Studio Code, en tant que développeur d’appareils, pour créer et tester un appareil IoT Plug-and-Play qui se connecte à IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 630f92bc8cf5fb89626bfe4de4e4a575045c961a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879353"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Utiliser Visual Studio Code pour créer un appareil IoT Plug-and-Play qui se connecte à IoT Central

Ce guide pratique vous montre comment effectuer les opérations suivantes :

* En tant qu’opérateur, ajouter et configurer un appareil réel dans votre application Azure IoT Central

* En tant que développeurs d’appareils, utiliser Visual Studio Code pour créer un appareil [IoT Plug-and-Play](https://aka.ms/iot-pnp-docs) qui se connecte à votre application IoT Central

Vous utilisez un [modèle de capacité d’appareil](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) pour définir l’appareil qui se connecte à IoT Central. Dans ce guide, vous utilisez un modèle qui définit un capteur environnemental.

Le développeur d’appareils utilise le modèle pour générer le code client de l’appareil et le fabricant l’utilise pour [créer un modèle d’appareil](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) dans IoT Central. Le modèle joue le rôle de contrat entre votre appareil et votre application IoT Central.

La section de ce guide qui explique comment compiler le code généré suppose que vous utilisez Windows.

Dans ce guide, vous apprendrez comment :

* Importer un modèle de capacité d’appareil dans un modèle d’appareil dans IoT Central
* Ajouter un tableau de bord au modèle, qui affiche les données de télémétrie de l’appareil
* Ajouter un appareil réel à partir du modèle
* Importer un modèle de capacité d’appareil dans Visual Studio Code
* Générer une application cliente d’appareil en C à partir du modèle
* Générer l’application cliente d’appareil en C et la connecter à IoT Central

## <a name="prerequisites"></a>Prérequis

Pour tester le code d’appareil dans le cadre de ce guide, vous avez besoin d’une application IoT Central créée à partir du modèle d’application en **préversion**. Si vous n’avez pas encore d’application IoT Central à utiliser, suivez le guide de démarrage rapide [Créer une application Azure IoT Central (fonctionnalités de préversion)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) :

Pour utiliser le modèle de capacité d’appareil dans le cadre de ce guide, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/download) : Visual Studio Code est disponible pour plusieurs plateformes.
* Extension Azure IoT Device Workbench pour Visual Studio Code. Pour installer l’extension Azure IoT Device Workbench dans VS Code, procédez comme suit :

    1. Dans VS Code, sélectionnez l’onglet **Extensions**.
    1. Recherchez **Azure IoT Device Workbench**.
    1. Sélectionnez **Installer**.

    > [!NOTE]
    > La version actuelle du générateur de code de l’extension ne prend pas en charge les types de schéma **Geopoint** et **Vector** ni les types de sémantique **Acceleration**, **Velocity** et **Location**. Ces types de schéma et de sémantique sont pris en charge par IoT Central.

    > [!NOTE]
    > Pour que le modèle de capacité d’appareil fonctionne avec IoT Central, toutes les interfaces correspondantes doivent être définies inline dans le même fichier.

Pour compiler le code C généré sous Windows dans le cadre de ce guide, vous avez besoin des éléments suivants :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : veillez à inclure le composant **Gestionnaire de package NuGet** et la charge de travail **Développement Desktop en C++** quand vous installez Visual Studio.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/) : quand vous installez **CMake**, sélectionnez l’option **Add CMake to the system PATH**.
* Une copie locale du SDK C Azure IoT :

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Pour suivre les instructions figurant à la fin de ce guide pratique, relatives aux connexions « device-first » (appareil en premier), vous devez également installer les éléments suivants :

* [Node.JS](https://nodejs.org)
* L’[outil dps-keygen](https://www.npmjs.com/package/dps-keygen) :

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Créer un modèle d’appareil

Créez un dossier nommé **pnp_app** dans le dossier **azure-iot-sdk-c** qui contient le SDK C Azure IoT que vous avez cloné. Téléchargez le modèle de capacité d’appareil [EnvironmentalSensor.capabilitymodel.json](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) à partir de GitHub et enregistrez le fichier dans le dossier **pnp_app**. Remplacez les deux instances de `<YOUR_COMPANY_NAME_HERE>` par votre nom. Utilisez uniquement les caractères a-z, A-Z, 0-9 et le trait de soulignement. Notez de valeur complète du champ `"@id"`, qui identifie votre modèle de capacité d’appareil de façon unique. Vous en aurez besoin plus tard. Ce modèle comprend deux interfaces :

* Interface commune **DeviceManagement**
* Interface personnalisée **EnvironmentalSensor**

Pour créer un modèle d’appareil pour le capteur environnemental dans IoT Central :

1. Dans la page **Modèles d’appareil**, sélectionnez **+ Nouveau**. Sélectionnez **Personnalisé**.

1. Entrez **Capteur environnemental** comme nom du modèle d’appareil.

1. Sélectionnez **Importer le modèle de capacité**. Recherchez ensuite le fichier **EnvironmentalSensor.capabilitymodel.json** que vous avez créé, puis sélectionnez **Ouvrir**. Les deux interfaces **Informations sur l’appareil** et **Capteur environnemental** s’affichent dans le **modèle de capacité de capteur environnemental**.

1. Sélectionnez **Vues**, puis **Visualisation de l’appareil**.

1. Dans la liste des champs que vous pouvez ajouter au tableau de bord, sélectionnez les deux valeurs **Télémétrie** (**Humidité** et **Température**), puis sélectionnez **Combiner**. Puis choisissez **Enregistrer**.

Vous disposez maintenant d’un modèle d’appareil qui utilise le modèle **Capteur environnemental** et offre un tableau de bord simple pour afficher les données de télémétrie à partir d’un appareil.

## <a name="publish-the-template-and-add-a-real-device"></a>Publier le modèle et ajouter un appareil réel

Pour publier le modèle et ajouter un appareil réel, accédez à la page **Modèles d’appareil** et sélectionnez le modèle **Capteur environnemental**. Sélectionnez **Publier**, passez en revue les informations, puis sélectionnez **Publier**.

Avant de connecter une application cliente, vous devez ajouter un appareil dans la page **Appareils** et obtenir ses informations de connexion :

1. Accédez à la page **Appareils**, puis sélectionnez **Capteur environnemental**. La page **Appareils** vous permet de gérer les appareils qui peuvent se connecter à votre application.

1. Pour ajouter un appareil réel, choisissez **+ Nouveau**, remplacez l’ID de l’appareil par **sensor01**, puis sélectionnez **Créer**. Veillez à ce que l’option **Simulé** soit définie sur **Désactivé**.

1. Dans la liste des appareils, sélectionnez l’appareil **Capteur environnemental**. Sélectionnez **Connecter**.

1. Notez les valeurs **ID de portée**, **ID de l’appareil** et **Clé primaire**. Sélectionnez **Fermer**.

## <a name="generate-a-device-client-application"></a>Générer une application cliente d’appareil

Vous allez utiliser Visual Studio Code pour générer une application cliente d’appareil squelette à partir du modèle de capacité d’appareil :

1. Lancez Visual Studio Code et ouvrez le dossier **pnp_app**.

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez**Générer le stub de code de l’appareil**.

1. Sélectionnez le fichier de modèle de capacité d’appareil **EnvironmentalSensor.capabilitymodel.json**.

1. Entrez le nom de projet **sensor_app**.

1. Choisissez le langage **ANSI C**.

1. Choisissez la cible **Projet CMake**.

1. Choisissez la méthode de connexion **Avec une clé symétrique DPS (service Device Provisioning)** .

Visual Studio Code ouvre une nouvelle fenêtre avec le code C généré dans le dossier **sensor_app**.

## <a name="add-connection-details-to-the-device-client"></a>Ajouter les informations de connexion au client de l’appareil

Pour ajouter les informations de connexion au client de l’appareil, ouvrez **main.c** dans le dossier qui contient votre code généré :

1. Remplacez `[DPS Id Scope]` par la valeur **ID de portée** que vous avez notée précédemment.

1. Remplacez `[DPS symmetric key]` par la valeur **Clé primaire** que vous avez notée précédemment.

1. Remplacez `[device registration Id]` par la valeur **ID de l’appareil** que vous avez notée précédemment.

1. Enregistrez les modifications.

## <a name="build-and-run-the-client"></a>Générer et exécuter le client

Pour générer et exécuter le client, vous devez personnaliser la build pour le SDK C Azure IoT :

1. Ouvrez le fichier **CMakeLists.txt** à la racine du dossier **azure-iot-sdk-c**.

1. Ajoutez la ligne suivante à la fin de ce fichier pour inclure la nouvelle application cliente dans la build :

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Enregistrez les modifications.

1. Ouvrez une invite de commandes et accédez au dossier **azure-iot-sdk-c**.

1. Exécutez les commandes suivantes pour générer l’application :

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Pour exécuter l’application, exécutez la commande suivante dans la même invite de commandes :

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Après quelques minutes, vous pouvez voir les données de télémétrie de l’appareil sur le tableau de bord de l’appareil dans votre application IoT Central.

## <a name="connect-device-first"></a>Connexion « device-first » (appareil en premier)

Vous pouvez connecter un appareil IoT Plug-and-Play par le biais d’une connexion « device-first » (appareil en premier) tel que décrit dans l’article sur la [connectivité](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Le processus de découverte s’effectue comme suit :

1. Association au modèle d’appareil s’il est déjà publié dans l’application IoT Central.

1. Récupération du modèle de capacité à partir du [référentiel public](https://aka.ms/ACFI) de modèles de capacité publiés et certifiés.

À l’aide de Visual Studio Code et de la commande **Générer le stub de code de l’appareil** mentionnée plus haut, vous pouvez suivre ces étapes pour effectuer une connexion « device-first » (appareil en premier) et récupérer automatiquement votre modèle de capacité d’appareil publié dans IoT Central à partir du référentiel public :

1. Utilisez un modèle de capacité d’appareil existant qui a été publié dans le référentiel public. Vous avez besoin du modèle de capacité d’appareil complet et vous devez noter son URN.

1. Suivez les étapes ci-dessus pour [générer une application cliente d’appareil](#generate-a-device-client-application) afin d’utiliser Visual Studio Code et générer le code de l’appareil.

1. À partir de votre application IoT Central, accédez à l’onglet **Administration**, puis sélectionnez la section **Connexion de l’appareil**. Notez les valeurs **ID de portée** et **Clé primaire** relatives à votre application.

    > [!NOTE]
    > La valeur **Clé primaire** figurant sur cette page est une signature d’accès partagé de groupe que vous pouvez utiliser pour générer plusieurs clés d’appareil pour votre application.

1. Utilisez l’outil [DPS keygen](https://www.npmjs.com/package/dps-keygen) pour générer une clé d’appareil à partir de la clé primaire que vous avez notée précédemment. Exécutez la commande suivante pour générer la clé d’appareil :

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Suivez les étapes de la section précédente [Ajouter des informations de connexion au client de l’appareil](#add-connection-details-to-the-device-client) pour ajouter les valeurs **ID de portée**, **Clé primaire** et **ID de l’appareil**.

1. Suivez les étapes de la section précédente pour [générer et exécuter le client](#build-and-run-the-client).

1. Vous voyez maintenant que l’appareil se connecte à votre application IoT Central et récupère automatiquement le modèle de capacité d’appareil à partir du référentiel public en tant que modèle d’appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un appareil réel à IoT Central, nous vous recommandons d’en apprendre davantage sur les modèles d’appareil en consultant [Configurer un modèle d’appareil](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
