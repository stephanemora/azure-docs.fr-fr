---
title: Connecter un kit cloud RX65N Renesas à Azure IoT Central – Démarrage rapide
description: Utilisez le logiciel intégré Azure RTOS pour connecter un appareil de kit cloud RX65N Renesas à Azure IoT et envoyer une télémétrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/04/2021
ms.openlocfilehash: 38227e47154e280fc34624b4f92a4f75261b7004
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121952"
---
# <a name="quickstart-connect-a-renesas-rx65n-cloud-kit-to-iot-central"></a>Démarrage rapide : Connecter un kit cloud RX65N Renesas à IoT Central

**S’applique à** : [Développement d’appareils intégrés](about-iot-develop.md#embedded-device-development)<br>
**Durée totale d’exécution** : 30 minutes

[![Parcourir le code](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RX65N_Cloud_Kit)

Dans le cadre de ce démarrage rapide, vous utilisez Azure RTOS pour connecter le kit cloud RX65N Renesas (ci-après, RX65N Renesas) à Azure IoT.

Vous allez effectuer les étapes suivantes :

* Installer un ensemble d’outils de développement intégrés pour la programmation d’un RX65N Renesas en C
* Créer une image et la flasher sur le RX65N Renesas
* Azure IoT Central permet de créer des composants cloud, d’afficher les propriétés, d’afficher la télémétrie des appareils et d’appeler des commandes directes

## <a name="prerequisites"></a>Prérequis

* PC exécutant Microsoft Windows 10
* [Git](https://git-scm.com/downloads) pour cloner le référentiel
* Matériel

    * Le [Kit cloud RX65N Renesas](https://www.renesas.com/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-cloud-kit-renesas-rx65n-cloud-kit) (RX65N Renesas)
    * 2 câbles USB 2.0 A mâle à mini-USB mâle
    * Wi-Fi 2,4 GHz

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Pour configurer votre environnement de développement, commencez par cloner un dépôt GitHub contenant toutes les ressources dont vous avez besoin pour le démarrage rapide. Ensuite, vous installez un ensemble d’outils de programmation.

### <a name="clone-the-repo-for-the-quickstart"></a>Cloner le dépôt pour le démarrage rapide

Clonez le référentiel suivant pour télécharger tous les exemples de code d’appareil, les scripts d’installation et les versions hors connexion de la documentation. Si vous avez déjà cloné ce dépôt dans le cadre d’un autre démarrage rapide, vous n’avez pas besoin de le refaire.

Pour cloner le référentiel, exécutez la commande suivante :

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installer les outils

Le référentiel cloné contient un script d’installation qui installe et configure les outils requis. Si vous avez installé ces outils dans le cadre d’un autre démarrage rapide d’appareil intégré, vous n’avez pas besoin de le refaire.

> [!NOTE]
> Le script d’installation installe les outils suivants :
> * [CMake](https://cmake.org) : générer
> * [RX GCC](http://gcc-renesas.com/downloads/get.php?f=rx/8.3.0.202004-gnurx/gcc-8.3.0.202004-GNURX-ELF.exe): compiler
> * [Termite](https://www.compuphase.com/software_termite.htm) : surveiller la sortie du port série pour les appareils connectés

Pour installer les outils :

1. À l’aide de l’Explorateur de fichiers, accédez au chemin d’accès suivant dans le dépôt et exécutez le script d’installation nommé *get-toolchain-rx.bat* :

    *getting-started\tools\get-toolchain-rx.bat*

1. Ajoutez le compilateur RX au chemin d’accès Windows :

   *%USERPROFILE%\AppData\Roaming\GCC pour Renesas RX 8.3.0.202004-GNURX-ELF\rx-elf\rx-elf\bin*

1. Après l’installation, ouvrez une nouvelle fenêtre de console pour identifier les modifications de configuration apportées par le script d’installation. Utilisez cette console pour accomplir les tâches de programmation restantes décrites dans ce démarrage rapide. Vous pouvez utiliser Windows CMD, PowerShell ou Git Bash pour Windows.
1. Exécutez les commandes suivantes pour vérifier que CMake version 3.14 ou ultérieure est installé, et que le chemin d’accès du compilateur RX est correctement configuré.

    ```shell
    cmake --version
    rx-elf-gcc --version
    ```
Pour installer les outils restants :

* Installez l’application [Renesas Flash Programmer](https://www.renesas.com/software-tool/renesas-flash-programmer-programming-gui). L’application Renesas Flash Programmer contient les pilotes et outils nécessaires pour flasher le RX65N Renesas via Renesas E2 Lite.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour connecter le RX65N Renesas à Azure, vous allez modifier un fichier de configuration pour les paramètres Wi-Fi et Azure IoT, régénérer l’image et la flasher sur l’appareil.

### <a name="add-configuration"></a>Ajouter une configuration

1. Ouvrez le fichier suivant dans un éditeur de texte :

    *getting-started\Renesas\RX65N_Cloud_Kit\app\azure_config.h*

1. Affectez aux constantes Wi-Fi les valeurs suivantes à partir de votre environnement local.

    |Nom de la constante|Valeur|
    |-------------|-----|
    |`WIFI_SSID` |{*Votre ssid Wi-Fi*}|
    |`WIFI_PASSWORD` |{*Votre mot de passe Wi-Fi*}|
    |`WIFI_MODE` |{*Une des valeurs de mode de Wi-Fi énumérées dans le fichier*}|

1. Définissez les constantes d’informations de l’appareil Azure IoT sur les valeurs que vous avez enregistrées après avoir créé les ressources Azure.

    |Nom de la constante|Valeur|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Valeur d’étendue de votre ID*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Valeur d’ID de votre appareil*}|
    |`IOT_DEVICE_SAS_KEY` |{*Valeur de votre clé primaire*}|

1. Enregistrez et fermez le fichier.

### <a name="build-the-image"></a>Créer l’image

1. Dans votre console ou dans l’Explorateur de fichiers, exécutez le script *rebuild.bat* à l’emplacement suivant pour générer l’image :

    *getting-started\Renesas\RX65N_Cloud_Kit\tools\rebuild.bat*

2. Une fois la génération terminée, vérifiez que le fichier binaire a été créé dans le chemin d’accès suivant :

    *getting-started\Renesas\RX65N_Cloud_Kit\build\app\rx65n_azure_iot.hex*

### <a name="connect-the-device"></a>Connecter l’appareil

> [!NOTE]
> Pour plus d’informations sur la configuration et la prise en main du RX65N Renesas, consultez [Démarrage rapide du kit cloud RX65N Renesas](https://www.renesas.com/document/man/quick-start-guide-renesas-rx65n-cloud-kit).

1. Suivez les étapes suivantes en vous référant à l’image ci-dessous.
    
    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/renesas-rx65n.jpg" alt-text="Localisez Reset, USB et E1/E2Lite sur la carte RX65N Renesas":::

1. Débranchez le connecteur **EJ2** de la carte pour activer le débogueur E2 Lite. Le connecteur se trouve sous le bouton **USER SW** .
    > [!WARNING] 
    > Si vous ne le débranchez pas, vous ne pourrez pas flasher l’appareil.

1. Connectez le **module Wi-Fi** à la **carte d’option cloud**.

1. À l’aide du premier câble mini-USB, connectez le port **USB série** du RX65N Renesas à votre ordinateur.

1. À l’aide du second câble mini-USB, connectez le port **USB E2 Lite** du RX65N Renesas à votre ordinateur.

### <a name="flash-the-image"></a>Flasher l’image

1. Lancez l’application *Renesas Flash Programmer* à partir du menu Démarrer.

2. Dans le menu *Fichier*, sélectionnez *Nouveau projet...* , puis entrez les paramètres suivants :
    * **Microcontrôleur** : RX65x
    * **Nom de projet** : RX65N
    * **Outil** : E2 Emulator Lite
    * **Interface** : FINE

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-new.png" alt-text="Capture d’écran de l’application Renesas Flash Programmer, Nouveau projet":::

3. Sélectionnez le bouton *Détails de l’outil*, puis accédez à l’onglet *Réinitialiser les paramètres*.

4. Sélectionnez *Réinitialiser le code confidentiel en tant que Hi-Z*, puis appuyez sur le bouton *OK*.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-reset.png" alt-text="Capture d’écran de l’application Renesas Flash Programmer, Réinitialiser les paramètres":::

5. Appuyez sur le bouton *Se connecter*, puis, quand vous y êtes invité, activez la case à cocher *Authentification automatique* et appuyez sur *OK*.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-auth.png" alt-text="Capture d’écran de l’application Renesas Flash Programmer, Authentification":::

6. Sélectionnez le bouton *Parcourir...* , puis recherchez le fichier *rx65n_azure_iot.hex* créé dans la section précédente.

7. Appuyez sur *Démarrer* pour commencer à flasher. Ce processus prend environ 10 secondes.

### <a name="confirm-device-connection-details"></a>Vérifier les détails de connexion de l’appareil

Vous pouvez utiliser l’application **Termite** pour surveiller la communication et vérifier que votre appareil est correctement configuré.
> [!TIP]
> Si vous rencontrez des problèmes lors de l’initialisation ou de la connexion de votre appareil après le flashage, consultez [Résolution des problèmes](troubleshoot-embedded-device-quickstarts.md).

1. Démarrez **Termite**.
1. Sélectionnez **Paramètres**.
1. Dans la boîte de dialogue **Paramètres du port série**, vérifiez les paramètres suivants et mettez à jour si nécessaire :
    * **Vitesse (en bauds)**  : 115 200
    * **Port**: port auquel votre RX65N Renesas est connecté. Si plusieurs options de port sont disponibles dans la liste déroulante, vous pouvez trouver le port approprié à utiliser. Ouvrez le **Gestionnaire d'appareils** Windows et affichez les **Ports** pour identifier le port à utiliser.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/termite-settings.png" alt-text="Capture d’écran des paramètres de port série dans l’application Termite":::

1. Sélectionnez OK.
1. Appuyez sur le bouton **Réinitialiser** de l’appareil.
1. Dans l’application **Termite**, vérifiez les valeurs de point de contrôle suivantes pour confirmer que l’appareil est initialisé et connecté à Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.21
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.76.76
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.79.214.107
        SNTP time update: May 21, 2021 20:24:10.76 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgrx65ncloud;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Laissez Termite ouvert pour surveiller la sortie de l’appareil dans les étapes suivantes.

## <a name="verify-the-device-status"></a>Vérifier l’état de l’appareil

Pour consulter l’état de l’appareil dans le portail IoT Central :
1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Vérifiez que l’**état de l'appareil** est mis à jour en **approvisionné**.
1. Vérifiez que le **modèle d’appareil** est mis à jour en **Guide de prise en main du kit cloud RX65N**.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-view-status.png" alt-text="Capture d’écran de l’état de l’appareil dans IoT Central":::

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Avec IoT Central, vous pouvez afficher le flux de télémétrie de votre appareil dans le cloud.

Pour afficher la télémétrie dans le portail IoT Central :

1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Sélectionnez l’appareil dans la liste.
1. Affichez la télémétrie lorsque l’appareil envoie des messages au cloud sous l’onglet **Vue d’ensemble**.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-telemetry.png" alt-text="Capture d’écran de la télémétrie de l’appareil dans IoT Central":::

    > [!NOTE]
    > Vous pouvez également surveiller la télémétrie à partir de l’appareil à l’aide de l’application Termite.

## <a name="call-a-direct-method-on-the-device"></a>Appeler une méthode directe sur l’appareil

Vous pouvez également utiliser IoT Central pour appeler une méthode directe que vous avez implémentée sur votre appareil. Les méthodes directes portent un nom et peuvent éventuellement avoir une charge utile JSON, une connexion configurable et un délai d’expiration de méthode. Dans cette section, vous appelez une méthode qui vous permet d’activer ou de désactiver un voyant.

Pour appeler une méthode dans le portail IoT Central :

1. Sélectionnez l’onglet **Commande** sur la page de l’appareil.
1. Dans la liste déroulante **État**, sélectionnez **Vrai**, puis **Exécuter**.  Le voyant doit s’allumer.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-invoke-method.png" alt-text="Capture d’écran de l’appel d’une méthode directe sur un appareil dans IoT Central":::

1. Dans la liste déroulante **État**, sélectionnez **Faux**, puis **Exécuter**. Le voyant doit s’éteindre.

## <a name="view-device-information"></a>Affichage des informations sur l’appareil

Vous pouvez afficher les informations de l’appareil à partir d’IoT Central.

Sélectionnez l’onglet **À propos de** sur la page de l’appareil.

:::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-about.png" alt-text="Capture d’écran des informations sur l’appareil dans IoT Central":::

## <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des problèmes pour générer le code d’appareil, flasher l’appareil ou le connecter, consultez [Résolution des problèmes](troubleshoot-embedded-device-quickstarts.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources Azure créées dans le cadre de ce démarrage rapide, vous pouvez les supprimer du portail IoT Central.

Pour supprimer la totalité de l’exemple d’application Azure IoT Central et tous ses appareils et ressources :
1. Sélectionnez **Administration** > **Votre application**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez créé une image personnalisée contenant un exemple de code Azure RTOS, puis flashé l’image sur l’appareil RX65N Renesas. Vous avez également utilisé le portail IoT Central pour créer des ressources Azure, connecter le RX65N Renesas en toute sécurité à Azure, afficher la télémétrie et envoyer des messages.

L’étape suivante consiste à explorer les articles ci-dessous pour savoir comment connecter des appareils à Azure IoT à l’aide des kits de développement logiciel (SDK) d’appareil IoT. 

> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS fournit aux fabricants OEM les composants permettant de sécuriser la communication et de créer du code et l’isolation des données à l’aide des mécanismes sous-jacents de protection matérielle MCU/MPU. Toutefois, chaque fabricant OEM est tenu de garantir que son appareil répond aux exigences de sécurité en constante évolution.

